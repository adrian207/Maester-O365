# Maester Deployment Framework - Architecture

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Version:** 0.9.0  
**Last Updated:** 2025-10-28

---

## Overview

This document describes the complete architecture of the Maester Deployment Framework, a production-ready solution for automated Microsoft 365 security testing, compliance validation, and automated remediation.

---

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Deployment Models](#deployment-models)
3. [Component Architecture](#component-architecture)
4. [Security Architecture](#security-architecture)
5. [Data Flow](#data-flow)
6. [Integration Points](#integration-points)
7. [Scalability & Performance](#scalability--performance)
8. [Disaster Recovery](#disaster-recovery)

---

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                     Maester Deployment Framework                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌────────────────────┐                                             │
│  │  Scheduler/Trigger │  (CronJob/Timer/EventBridge)                │
│  └─────────┬──────────┘                                             │
│            │                                                          │
│            ↓                                                          │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │              Maester Test Runner                           │    │
│  │  ┌──────────────────────────────────────────────────┐     │    │
│  │  │  • PowerShell 7.x Runtime                        │     │    │
│  │  │  • Maester Module + EIDSCA Tests                 │     │    │
│  │  │  • Microsoft Graph PowerShell SDK                │     │    │
│  │  │  • Workload Identity / Managed Identity          │     │    │
│  │  └──────────────────────────────────────────────────┘     │    │
│  └────────────────────┬───────────────────────────────────────┘    │
│                       │                                              │
│                       ↓                                              │
│              Microsoft Graph API                                     │
│              (Microsoft 365 Tenant)                                  │
│                       │                                              │
│                       ↓                                              │
│  ┌────────────────────┴───────────────────────────────────────┐    │
│  │                    Results Processing                       │    │
│  └────┬──────────────┬───────────────┬────────────────┬───────┘    │
│       │              │               │                │              │
│       ↓              ↓               ↓                ↓              │
│  ┌─────────┐  ┌─────────────┐ ┌──────────────┐ ┌──────────────┐  │
│  │ Report  │  │ Compliance  │ │ Remediation  │ │ Notification │  │
│  │ Server  │  │   Mapper    │ │   Engine     │ │     Hub      │  │
│  └────┬────┘  └──────┬──────┘ └──────┬───────┘ └──────┬───────┘  │
│       │              │                │                │            │
│       ↓              ↓                ↓                ↓            │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │              Storage & Persistence Layer                     │  │
│  │  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐   │  │
│  │  │ PostgreSQL  │  │ Cloud Storage│  │ Blob/S3/GCS      │   │  │
│  │  │ (Metadata)  │  │ (Reports)    │  │ (Archive)        │   │  │
│  │  └─────────────┘  └──────────────┘  └──────────────────┘   │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │              Monitoring & Observability                      │  │
│  │  • Prometheus • Grafana • Application Insights • CloudWatch  │  │
│  └─────────────────────────────────────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Deployment Models

### 1. vSphere Kubernetes (Primary)

**Target Platform**: VMware vSphere 7.0+ with Tanzu, RKE2, or Vanilla Kubernetes

```
┌─────────────────────────────────────────────────────────┐
│             vSphere Infrastructure                       │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Kubernetes Cluster                       │         │
│  │   ┌────────────────────────────────────┐   │         │
│  │   │  Master Nodes (3x HA)              │   │         │
│  │   │  - 8 vCPU, 16GB RAM each           │   │         │
│  │   │  - Anti-affinity rules             │   │         │
│  │   └────────────────────────────────────┘   │         │
│  │   ┌────────────────────────────────────┐   │         │
│  │   │  Worker Nodes (3-6x)               │   │         │
│  │   │  - 16 vCPU, 32GB RAM each          │   │         │
│  │   │  - Auto-scaling capable            │   │         │
│  │   └────────────────────────────────────┘   │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   vSAN / NFS Storage                       │         │
│  │   - 500GB+ for reports & data              │         │
│  │   - CSI driver integration                 │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   NSX-T / Standard Networking              │         │
│  │   - Load balancer (MetalLB/NSX-T)          │         │
│  │   - Network policies                       │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Harbor Container Registry (Optional)      │         │
│  │   - Image scanning                         │         │
│  │   - Replication to cloud                   │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**Deployment Options**:
- **Tanzu Kubernetes Grid**: VMware-supported, enterprise-grade
- **RKE2**: Rancher Kubernetes, hardened, FIPS-compliant
- **Vanilla Kubernetes**: Open-source, maximum flexibility

---

### 2. Azure Serverless

**Target Platform**: Azure Functions + Container Apps

```
┌─────────────────────────────────────────────────────────┐
│              Azure Serverless Architecture               │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Azure Functions (PowerShell)             │         │
│  │   - Timer trigger (scheduled tests)        │         │
│  │   - Durable Functions (long-running)       │         │
│  │   - Consumption/Premium plan               │         │
│  │   - Managed Identity auth                  │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│               ↓                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Container Apps                           │         │
│  │   - Report Server (scale to zero)          │         │
│  │   - Remediation Engine                     │         │
│  │   - Notification Hub                       │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│  ┌────────────┴───────────────────────────────┐         │
│  │   Azure Services                           │         │
│  │   ┌──────────────────────────────────┐     │         │
│  │   │ Cosmos DB (Serverless)           │     │         │
│  │   │ Blob Storage (Reports)           │     │         │
│  │   │ Key Vault (Secrets)              │     │         │
│  │   │ Logic Apps (Notifications)       │     │         │
│  │   │ Application Insights (Monitor)   │     │         │
│  │   └──────────────────────────────────┘     │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**Cost**: ~$35-90/month  
**Best For**: Cloud-first organizations, cost-sensitive deployments

---

### 3. AWS Serverless

**Target Platform**: AWS Lambda + Fargate

```
┌─────────────────────────────────────────────────────────┐
│               AWS Serverless Architecture                │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Lambda Functions                         │         │
│  │   - PowerShell runtime (container)         │         │
│  │   - EventBridge scheduled trigger          │         │
│  │   - Step Functions orchestration           │         │
│  │   - IAM roles for auth                     │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│               ↓                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Fargate (Serverless Containers)          │         │
│  │   - Report Server                          │         │
│  │   - API Gateway integration                │         │
│  │   - Application Load Balancer              │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│  ┌────────────┴───────────────────────────────┐         │
│  │   AWS Services                             │         │
│  │   ┌──────────────────────────────────┐     │         │
│  │   │ DynamoDB (On-Demand)             │     │         │
│  │   │ S3 (Reports & Archive)           │     │         │
│  │   │ Secrets Manager                  │     │         │
│  │   │ SNS/SES (Notifications)          │     │         │
│  │   │ CloudWatch (Monitoring)          │     │         │
│  │   └──────────────────────────────────┘     │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**Cost**: ~$40-85/month  
**Best For**: AWS-native organizations

---

### 4. Google Cloud Serverless

**Target Platform**: Cloud Functions + Cloud Run

```
┌─────────────────────────────────────────────────────────┐
│              GCP Serverless Architecture                 │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Cloud Functions (2nd Gen)                │         │
│  │   - Cloud Scheduler trigger                │         │
│  │   - Workload Identity                      │         │
│  │   - Cloud Tasks for long runs              │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│               ↓                                           │
│  ┌────────────────────────────────────────────┐         │
│  │   Cloud Run                                │         │
│  │   - Fully managed containers               │         │
│  │   - Scale to zero                          │         │
│  │   - HTTPS endpoints                        │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│  ┌────────────┴───────────────────────────────┐         │
│  │   GCP Services                             │         │
│  │   ┌──────────────────────────────────┐     │         │
│  │   │ Firestore (Serverless)           │     │         │
│  │   │ Cloud Storage (Reports)          │     │         │
│  │   │ Secret Manager                   │     │         │
│  │   │ Pub/Sub (Notifications)          │     │         │
│  │   │ Cloud Logging (Monitoring)       │     │         │
│  │   └──────────────────────────────────┘     │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**Cost**: ~$35-80/month  
**Best For**: GCP-native organizations

---

### 5. Docker Compose (Standalone)

**Target Platform**: Single server/VM with Docker

```
┌─────────────────────────────────────────────────────────┐
│            Docker Compose Deployment                     │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  Docker Host (Linux/Windows Server)                      │
│  ┌────────────────────────────────────────────┐         │
│  │   maester-runner (scheduled via cron)      │         │
│  │   maester-report-server:8080               │         │
│  │   maester-notification-hub                 │         │
│  │   maester-remediation-engine               │         │
│  │   postgresql:5432                          │         │
│  │   redis:6379                               │         │
│  │   nginx (reverse proxy)                    │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
│  Volumes:                                                 │
│  - ./data/postgres (database)                            │
│  - ./data/reports (local reports)                        │
│  - ./config (configurations)                             │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**Cost**: $0 incremental (uses existing infrastructure)  
**Best For**: Testing, small organizations, air-gapped environments

---

## Component Architecture

### 1. Maester Test Runner

**Purpose**: Execute Maester tests against Microsoft 365 tenant

**Technology Stack**:
- PowerShell 7.4+
- Maester PowerShell Module
- Pester 5.x
- Microsoft Graph PowerShell SDK
- Az.Accounts (for Workload Identity)

**Key Features**:
- Scheduled test execution
- Parallel test execution
- Test filtering by tags/categories
- Custom test library support
- What-If analysis for Conditional Access
- Report generation (HTML/JSON/CSV/Markdown)

**Resource Requirements**:
- CPU: 2-4 vCPU
- Memory: 4-8GB RAM
- Storage: 10GB (container + cache)
- Network: Outbound HTTPS to Microsoft Graph

**Scaling**: Horizontal (multiple pods/containers)

---

### 2. Report Server

**Purpose**: Web UI for viewing and searching test results

**Technology Stack**:
- Frontend: React 18 + TypeScript
- Backend: Node.js 20 + Express
- Database: PostgreSQL 15
- Cache: Redis 7

**Key Features**:
- Interactive HTML reports
- Historical trend analysis
- Advanced search and filtering
- Compliance framework views
- REST API for integrations
- Real-time updates (WebSocket)
- Export to PDF/Excel

**Resource Requirements**:
- CPU: 2-4 vCPU
- Memory: 4-8GB RAM
- Storage: 100GB+ (reports + database)

**Scaling**: Horizontal with load balancer

---

### 3. Compliance Mapper

**Purpose**: Map test results to compliance frameworks

**Technology Stack**:
- Python 3.12
- FastAPI
- YAML configuration files
- PostgreSQL

**Supported Frameworks**:
- NIST 800-53 Rev 5
- CIS Microsoft 365 Benchmarks
- ISO 27001:2022
- HIPAA Security Rule
- PCI-DSS v4.0
- SOC 2 Trust Services
- CMMC 2.0 (Levels 1-3)
- FedRAMP
- HITRUST CSF

**Key Features**:
- Automated control mapping
- Gap analysis
- Evidence collection
- Compliance scoring
- Trend tracking

**Resource Requirements**:
- CPU: 1-2 vCPU
- Memory: 2-4GB RAM
- Storage: 5GB

---

### 4. Remediation Engine

**Purpose**: Automated and semi-automated security fixes

**Technology Stack**:
- PowerShell 7.4+
- Microsoft Graph PowerShell SDK
- PostgreSQL (remediation database)

**Remediation Tiers**:
1. **Automated**: Low-risk, immediate execution
2. **Semi-Automated**: Medium-risk, approval required
3. **Guided Manual**: High-risk, step-by-step instructions

**Key Features**:
- Pre-flight validation
- Dry-run capability
- Approval workflows
- Configuration snapshots
- Automatic rollback
- Audit trail
- Ticketing integration

**Resource Requirements**:
- CPU: 2-4 vCPU
- Memory: 4-8GB RAM
- Storage: 20GB (snapshots)

**See**: [Remediation Architecture](architecture/REMEDIATION-ARCHITECTURE.md)

---

### 5. Notification Hub

**Purpose**: Multi-channel notification orchestration

**Technology Stack**:
- Node.js 20
- Redis (queue management)
- Template engine (Handlebars)

**Supported Channels**:
- Email (SMTP)
- Microsoft Teams (Adaptive Cards)
- Slack (Block Kit)
- Webhooks (generic)
- PagerDuty
- ServiceNow
- Custom integrations

**Key Features**:
- Smart routing by severity
- Rate limiting
- Retry logic
- Alert deduplication
- Template customization
- Multi-language support

**Resource Requirements**:
- CPU: 1-2 vCPU
- Memory: 2-4GB RAM
- Storage: 5GB

---

## Security Architecture

### Authentication & Authorization

#### Workload Identity Federation (Recommended)

```
┌─────────────────────────────────────────────────────────┐
│         Workload Identity Federation Flow                │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  1. Kubernetes Pod starts with ServiceAccount            │
│     ↓                                                     │
│  2. K8s projects OIDC token to pod volume                │
│     ↓                                                     │
│  3. Application reads projected token                    │
│     ↓                                                     │
│  4. Exchange token with Azure AD                         │
│     ↓                                                     │
│  5. Receive Azure AD access token                        │
│     ↓                                                     │
│  6. Call Microsoft Graph API                             │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

**Advantages**:
- ✅ No secrets in configuration
- ✅ Automatic token rotation
- ✅ Native cloud integration
- ✅ Reduced attack surface
- ✅ Simplified secret management

**Microsoft Graph Permissions** (Least Privilege):
```
Application Permissions (Read-Only):
- Organization.Read.All
- Policy.Read.All
- Directory.Read.All
- RoleManagement.Read.Directory
- ConditionalAccess.Read.All
- User.Read.All
- Group.Read.All

Remediation Permissions (Additional):
- Policy.ReadWrite.ConditionalAccess
- Policy.ReadWrite.AuthenticationMethod
- Directory.ReadWrite.Limited
```

---

### Network Security

#### Network Segmentation

```
┌─────────────────────────────────────────────────────────┐
│              Network Security Zones                      │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌────────────────────────────────────────────┐         │
│  │  Public Zone (DMZ)                         │         │
│  │  - Ingress Controller (HTTPS only)         │         │
│  │  - Web Application Firewall                │         │
│  │  - Rate limiting                            │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│               ↓                                           │
│  ┌────────────────────────────────────────────┐         │
│  │  Application Zone                          │         │
│  │  - Report Server                           │         │
│  │  - API Gateway                             │         │
│  │  - Network Policies enforced               │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│               ↓                                           │
│  ┌────────────────────────────────────────────┐         │
│  │  Processing Zone                           │         │
│  │  - Maester Runner                          │         │
│  │  - Remediation Engine                      │         │
│  │  - No inbound access                       │         │
│  │  - Outbound to Microsoft Graph only        │         │
│  └────────────┬───────────────────────────────┘         │
│               │                                           │
│               ↓                                           │
│  ┌────────────────────────────────────────────┐         │
│  │  Data Zone                                 │         │
│  │  - PostgreSQL                              │         │
│  │  - Redis                                   │         │
│  │  - Encrypted at rest                       │         │
│  │  - Access from application zone only       │         │
│  └────────────────────────────────────────────┘         │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

---

### Data Security

#### Encryption

**At Rest**:
- Database encryption (TDE)
- Disk encryption (LUKS/BitLocker)
- Cloud storage encryption (AES-256)
- Secrets encryption (Key Vault/Secrets Manager)

**In Transit**:
- TLS 1.3 for all communications
- Certificate management (cert-manager)
- mTLS between services (optional)

#### Data Retention

```yaml
retention_policies:
  test_results:
    local: 90 days
    cloud_archive: 7 years
    
  audit_logs:
    retention: 7 years
    immutable: true
    
  remediation_snapshots:
    retention: 1 year
    
  compliance_evidence:
    retention: 7 years
    encrypted: true
```

---

## Data Flow

### Test Execution Flow

```
┌─────────────────────────────────────────────────────────┐
│            Test Execution Data Flow                      │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  Trigger (CronJob/Timer)                                 │
│    ↓                                                      │
│  Maester Runner Pod starts                               │
│    ↓                                                      │
│  Acquire authentication token (Workload Identity)        │
│    ↓                                                      │
│  Load test configuration (ConfigMap)                     │
│    ↓                                                      │
│  Execute Maester tests                                   │
│    ├─→ Call Microsoft Graph API (read operations)       │
│    ├─→ Execute Pester tests                             │
│    └─→ Collect results                                   │
│       ↓                                                   │
│  Generate reports (HTML/JSON/CSV)                        │
│    ↓                                                      │
│  ┌─────────┴──────────┬──────────────┬──────────┐      │
│  ↓                     ↓              ↓          ↓       │
│  Store in            Upload to     Send to    Trigger    │
│  PostgreSQL          Cloud Storage Compliance Remediation│
│  (metadata)          (archive)     Mapper     Engine     │
│    ↓                     ↓              ↓          ↓       │
│  Report Server       Backup         Map to     Evaluate   │
│  UI update           complete       frameworks fixes      │
│    ↓                                    ↓          ↓       │
│  WebSocket           Compliance     Create       │        │
│  notification        reports         remediation │        │
│                      generated       tickets     ↓        │
│                         ↓                        │         │
│                      Notification Hub ←──────────┘        │
│                         ↓                                  │
│                      Send notifications                    │
│                      (Email/Teams/Slack/Webhooks)          │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

---

## Integration Points

### Microsoft Graph API

**Endpoints Used**:
- `/v1.0/organization`
- `/v1.0/policies/conditionalAccessPolicies`
- `/v1.0/policies/authenticationMethodsPolicy`
- `/v1.0/directory/roleDefinitions`
- `/v1.0/users`
- `/v1.0/groups`
- `/v1.0/security/secureScores`

**Rate Limiting**: Respects Microsoft Graph throttling (429 responses)

---

### External Integrations

```yaml
integrations:
  ticketing:
    - ServiceNow
    - Jira
    - Azure DevOps Boards
    
  notifications:
    - Microsoft Teams
    - Slack
    - Email (SMTP)
    - PagerDuty
    - OpsGenie
    
  siem:
    - Azure Sentinel
    - Splunk
    - ELK Stack
    
  monitoring:
    - Prometheus
    - Grafana
    - Application Insights
    - CloudWatch
    - Datadog
```

---

## Scalability & Performance

### Performance Targets

| Metric | Target | Notes |
|--------|--------|-------|
| Test Execution | < 10 min | For 87 standard tests |
| Report Generation | < 2 min | HTML + PDF + Excel |
| API Response | < 500ms | 95th percentile |
| Dashboard Load | < 2 sec | Initial page load |
| Concurrent Users | 50+ | Report server |
| Cold Start (Serverless) | < 30 sec | Function initialization |

### Scaling Strategies

**Horizontal Scaling**:
- Report Server: 2-10 replicas
- Notification Hub: 2-5 replicas
- Compliance Mapper: 2-4 replicas

**Vertical Scaling**:
- Maester Runner: Increase CPU for faster execution
- PostgreSQL: Increase memory for larger datasets

**Auto-Scaling**:
```yaml
autoscaling:
  report_server:
    min_replicas: 2
    max_replicas: 10
    target_cpu: 70%
    target_memory: 80%
```

---

## Disaster Recovery

### Backup Strategy

**Database Backups**:
- Automated daily backups
- Point-in-time recovery (7 days)
- Cross-region replication
- Tested restore procedures

**Report Backups**:
- Real-time sync to cloud storage
- Multi-region replication
- Lifecycle policies (archive after 90 days)

**Configuration Backups**:
- GitOps (Kubernetes manifests in Git)
- Terraform state in remote backend
- Secrets in Key Vault/Secrets Manager

### Recovery Time Objectives

| Component | RTO | RPO | Strategy |
|-----------|-----|-----|----------|
| Test Execution | 1 hour | 24 hours | Redeploy runner |
| Report Server | 30 min | 5 min | Multi-region deployment |
| Database | 15 min | 5 min | Automated failover |
| Cloud Storage | 0 min | 0 min | Multi-region replication |

---

## Deployment Architecture Comparison

| Feature | vSphere K8s | Azure Serverless | AWS Serverless | Docker Compose |
|---------|-------------|------------------|----------------|----------------|
| **Setup Time** | 4-6 hours | 1-2 hours | 1-2 hours | 30 minutes |
| **Complexity** | High | Medium | Medium | Low |
| **Monthly Cost** | $20-50* | $35-90 | $40-85 | $0* |
| **Scalability** | Excellent | Excellent | Excellent | Limited |
| **HA Built-in** | Yes | Yes | Yes | No |
| **Best For** | Enterprise | Cloud-first | AWS shops | Testing/Small |

*Plus existing infrastructure costs

---

## Next Steps

- Review [Deployment Guides](../deployment-guides/) for platform-specific instructions
- See [Security Best Practices](../operations/security-best-practices.md)
- Read [Operations Guide](../operations/README.md)

---

**Document Version**: 0.9.0  
**Last Review**: 2025-10-28  
**Next Review**: Before v1.0.0 release

