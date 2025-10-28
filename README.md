# Maester Deployment Framework

> **Enterprise-grade deployment automation for [Maester](https://maester.dev) - Microsoft 365 Security Test Automation**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-0.9.0-green.svg)](CHANGELOG.md)
[![Maester](https://img.shields.io/badge/Maester-Compatible-purple.svg)](https://maester.dev)

**Author:** Adrian Johnson <adrian207@gmail.com>

---

## 🎯 Overview

The Maester Deployment Framework provides production-ready, multi-platform deployment solutions for automated Microsoft 365 security testing using [Maester](https://maester.dev). This framework enables continuous security monitoring, compliance validation, and automated reporting across your Microsoft 365 tenant.

### Key Capabilities

- **🏢 Enterprise Deployments**: vSphere (Tanzu/RKE2/Vanilla K8s), AKS, EKS, GKE
- **☁️ Serverless Options**: Azure Functions, AWS Lambda, Google Cloud Functions
- **🐳 Containerized**: Docker Compose for rapid deployment
- **🔐 Zero-Trust Security**: Workload Identity Federation (no secrets!)
- **📊 Comprehensive Compliance**: NIST 800-53, CIS, ISO 27001, HIPAA, PCI-DSS, SOC 2, CMMC
- **🔔 Multi-Channel Notifications**: Email, Microsoft Teams, Slack, Webhooks
- **💾 Hybrid Storage**: Embedded web server + Cloud backup
- **🔄 Auto-Updates**: Periodic updates for Maester and test definitions

---

## 🚀 Quick Start

### Prerequisites

- Microsoft 365 tenant with appropriate permissions
- Azure AD App Registration (for authentication)
- One of:
  - vSphere 7.0+ cluster
  - Kubernetes cluster (any distribution)
  - Azure/AWS/GCP account (for serverless)
  - Docker + Docker Compose

### 5-Minute Deployment (Docker Compose)

```bash
# Clone the repository
git clone https://github.com/your-org/maester-deployment.git
cd maester-deployment

# Configure environment
cp .env.example .env
# Edit .env with your Azure AD credentials

# Deploy
docker-compose up -d

# Access web UI
open http://localhost:8080
```

For production deployments, see our [Deployment Guides](docs/deployment-guides/).

---

## 📋 Deployment Options

| Platform | Complexity | Setup Time | Best For | Guide |
|----------|-----------|------------|----------|-------|
| **Docker Compose** | ⭐ Low | ~30 min | Testing, Small Orgs | [Guide](docs/deployment-guides/docker-compose.md) |
| **Azure Functions** | ⭐⭐ Medium | ~1 hour | Cloud-First, Cost-Sensitive | [Guide](docs/deployment-guides/azure-serverless.md) |
| **vSphere Tanzu** | ⭐⭐⭐ High | ~4 hours | Enterprise, On-Premise | [Guide](docs/deployment-guides/vsphere-tanzu.md) |
| **Azure AKS** | ⭐⭐ Medium | ~2 hours | Azure-Native | [Guide](docs/deployment-guides/azure-aks.md) |
| **AWS EKS** | ⭐⭐ Medium | ~2 hours | AWS-Native | [Guide](docs/deployment-guides/aws-eks.md) |
| **GCP GKE** | ⭐⭐ Medium | ~2 hours | GCP-Native | [Guide](docs/deployment-guides/gcp-gke.md) |

---

## 🏗️ Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Maester Deployment                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   Maester    │───▶│   Report     │───▶│ Notification │  │
│  │   Runner     │    │   Server     │    │     Hub      │  │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘  │
│         │                    │                    │           │
│         │                    │                    │           │
│         ▼                    ▼                    ▼           │
│  ┌─────────────────────────────────────────────────────┐   │
│  │         Microsoft Graph API (M365 Tenant)            │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │ Compliance   │    │   Cloud      │    │  Monitoring  │  │
│  │   Mapper     │    │   Storage    │    │   & Alerts   │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

For detailed architecture, see [ARCHITECTURE.md](docs/ARCHITECTURE.md).

---

## ✨ Features

### Security Testing
- ✅ **40+ EIDSCA Tests**: Pre-configured Entra ID Security Config Analyzer tests
- ✅ **Conditional Access**: Policy validation and What-If analysis
- ✅ **Custom Tests**: PowerShell-based Pester tests
- ✅ **Continuous Monitoring**: Scheduled test execution
- ✅ **Regression Testing**: Validate changes before deployment

### Compliance & Reporting
- ✅ **Multi-Framework Support**: NIST 800-53, CIS, ISO 27001, HIPAA, PCI-DSS, SOC 2, CMMC
- ✅ **Automated Mapping**: Tests mapped to compliance controls
- ✅ **Evidence Collection**: Automated audit-ready evidence packages
- ✅ **Gap Analysis**: Identify compliance gaps and remediation steps
- ✅ **Trend Analysis**: Historical compliance score tracking

### Notifications
- ✅ **Email**: HTML-formatted reports with embedded charts
- ✅ **Microsoft Teams**: Adaptive Cards with interactive actions
- ✅ **Slack**: Rich message blocks with threaded updates
- ✅ **Webhooks**: Custom integrations (PagerDuty, ServiceNow, etc.)
- ✅ **Smart Routing**: Severity-based notification channels

### Storage & Access
- ✅ **Embedded Web Server**: Real-time report viewing with search
- ✅ **Cloud Backup**: Azure Blob, AWS S3, Google Cloud Storage
- ✅ **REST API**: Programmatic access to test results
- ✅ **Multi-Format Export**: HTML, PDF, Excel, JSON, CSV

### Security
- ✅ **Workload Identity Federation**: No secrets in configuration
- ✅ **Managed Identities**: Azure/AWS/GCP native authentication
- ✅ **Least Privilege**: Minimal Microsoft Graph permissions
- ✅ **Audit Logging**: Complete activity tracking
- ✅ **Encryption**: At-rest and in-transit encryption

---

## 📊 Compliance Frameworks

The framework includes comprehensive mappings for:

| Framework | Controls | Coverage | Evidence |
|-----------|----------|----------|----------|
| **NIST 800-53 Rev 5** | 1,194 | 342 applicable | ✅ Automated |
| **CIS Microsoft 365** | 150+ | Full | ✅ Automated |
| **ISO 27001:2022** | 93 (Annex A) | Full | ✅ Automated |
| **HIPAA Security Rule** | 45 | Full | ✅ Automated |
| **PCI-DSS v4.0** | 12 requirements | Applicable | ✅ Automated |
| **SOC 2 Type II** | 5 Trust Services | Full | ✅ Automated |
| **CMMC 2.0** | Level 1-3 | Full | ✅ Automated |

See [Compliance Documentation](docs/compliance/) for detailed mappings.

---

## 🗂️ Repository Structure

```
maester-deployment/
├── docs/                       # Documentation
│   ├── deployment-guides/      # Platform-specific guides
│   ├── architecture/           # Architecture documentation
│   ├── compliance/            # Compliance framework docs
│   └── operations/            # Operations guides
├── docker/                    # Docker images
│   ├── maester-runner/        # Test runner container
│   ├── report-server/         # Web UI and API
│   ├── notification-hub/      # Notification orchestrator
│   └── compliance-mapper/     # Compliance engine
├── terraform/                 # Infrastructure as Code
│   ├── modules/              # Reusable modules
│   └── environments/         # Environment configs
├── kubernetes/               # Kubernetes manifests
│   ├── base/                # Base resources
│   ├── overlays/            # Kustomize overlays
│   └── helm/                # Helm charts
├── serverless/              # Serverless deployments
│   ├── azure-functions/     # Azure Functions
│   ├── aws-lambda/          # AWS Lambda
│   └── gcp-functions/       # Google Cloud Functions
├── compliance/              # Compliance mappings
│   └── frameworks/          # Framework definitions
├── tests/                   # Custom Maester tests
│   ├── examples/           # Example tests
│   └── templates/          # Test templates
└── scripts/                # Utility scripts
    ├── setup/             # Setup automation
    └── maintenance/       # Maintenance scripts
```

---

## 🔧 Configuration

### Environment Variables

```bash
# Azure AD Authentication
AZURE_TENANT_ID=your-tenant-id
AZURE_CLIENT_ID=your-client-id

# Test Configuration
TEST_SCHEDULE="0 2 * * *"           # Daily at 2 AM
TEST_TAGS="EIDSCA,CA,MFA"          # Test categories
REPORT_RETENTION_DAYS=90

# Notifications
NOTIFICATION_EMAIL=security@company.com
TEAMS_WEBHOOK_URL=https://...
SLACK_WEBHOOK_URL=https://...

# Storage
CLOUD_STORAGE_PROVIDER=azure       # azure|aws|gcp
STORAGE_ACCOUNT_NAME=maesterreports
```

See [Configuration Guide](docs/operations/configuration.md) for full reference.

---

## 📖 Documentation

### Getting Started
- [Quick Start Guide](docs/quick-start.md)
- [Prerequisites](docs/prerequisites.md)
- [Azure AD Setup](docs/azure-ad-setup.md)

### Deployment Guides
- [Docker Compose](docs/deployment-guides/docker-compose.md)
- [vSphere Tanzu](docs/deployment-guides/vsphere-tanzu.md)
- [vSphere Vanilla Kubernetes](docs/deployment-guides/vsphere-vanilla.md)
- [Azure Functions (Serverless)](docs/deployment-guides/azure-serverless.md)
- [AWS Lambda (Serverless)](docs/deployment-guides/aws-serverless.md)
- [Azure AKS](docs/deployment-guides/azure-aks.md)
- [AWS EKS](docs/deployment-guides/aws-eks.md)
- [Google GKE](docs/deployment-guides/gcp-gke.md)

### Operations
- [Configuration Reference](docs/operations/configuration.md)
- [Monitoring & Alerting](docs/operations/monitoring.md)
- [Backup & Recovery](docs/operations/backup-recovery.md)
- [Troubleshooting](docs/operations/troubleshooting.md)
- [Updates & Maintenance](docs/operations/updates.md)

### Development
- [Contributing Guide](CONTRIBUTING.md)
- [Custom Tests](docs/development/custom-tests.md)
- [API Documentation](docs/api/README.md)
- [Architecture Decisions](docs/architecture/adr/)

---

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Setup

```bash
# Clone repository
git clone https://github.com/your-org/maester-deployment.git
cd maester-deployment

# Install development dependencies
./scripts/setup/dev-setup.sh

# Run tests
./scripts/test/run-tests.sh
```

---

## 📋 Roadmap

### v0.9.0 (Current) - Pre-Release
- ✅ Core deployment framework
- ✅ vSphere support (Tanzu/Vanilla/RKE2)
- ✅ Kubernetes deployments
- ✅ Serverless options (Azure/AWS/GCP)
- ✅ Compliance framework mappings
- ✅ Multi-channel notifications

### v1.0.0 - General Availability
- ⬜ Production hardening
- ⬜ Performance optimizations
- ⬜ Extended testing coverage
- ⬜ Enhanced documentation
- ⬜ Community feedback integration

### v1.1.0 - Enhanced Features
- ⬜ Multi-tenant support
- ⬜ Advanced dashboards
- ⬜ Automated remediation workflows
- ⬜ GitOps integration (ArgoCD/Flux)
- ⬜ Cost optimization recommendations

See [ROADMAP.md](ROADMAP.md) for detailed planning.

---

## 📊 Performance & Scalability

| Metric | Target | Typical |
|--------|--------|---------|
| Test Execution Time | < 10 min | 5-7 min |
| Report Generation | < 2 min | 30-60 sec |
| API Response Time | < 500ms | 100-200ms |
| Cold Start (Serverless) | < 30 sec | 5-15 sec |
| Concurrent Users | 50+ | N/A |

---

## 💰 Cost Estimates

### Docker Compose (On-Premise)
- **Infrastructure**: Existing server/VM
- **Monthly Cost**: $0 incremental

### vSphere Deployment
- **Infrastructure**: Existing vSphere investment
- **VMs**: ~144 vCPU, ~224GB RAM total
- **Storage**: ~500GB
- **Monthly Cost**: $0 incremental + $20-50 cloud backup

### Azure Serverless
- **Azure Functions**: ~$10-30/month
- **Container Apps**: ~$20-40/month (scale to zero)
- **Storage**: ~$5-20/month
- **Total**: **$35-90/month**

### AWS Serverless
- **Lambda**: ~$10-25/month
- **Fargate**: ~$25-45/month
- **S3**: ~$5-15/month
- **Total**: **$40-85/month**

---

## 🔒 Security

### Reporting Security Issues

Please report security vulnerabilities to security@your-domain.com. Do not create public GitHub issues for security vulnerabilities.

### Security Best Practices

- ✅ Use Workload Identity Federation (no secrets)
- ✅ Enable encryption at rest and in transit
- ✅ Implement least privilege access
- ✅ Regular security updates
- ✅ Audit logging enabled
- ✅ Network isolation

See [SECURITY.md](SECURITY.md) for detailed security practices.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- [Maester Team](https://maester.dev) for the excellent security testing framework
- [Microsoft Graph](https://docs.microsoft.com/graph) for comprehensive APIs
- [Pester](https://pester.dev) for PowerShell testing framework
- Community contributors and testers

---

## 📞 Support

### Community Support
- [GitHub Issues](https://github.com/your-org/maester-deployment/issues)
- [GitHub Discussions](https://github.com/your-org/maester-deployment/discussions)
- [Discord Community](https://discord.gg/maester)

### Documentation
- [Official Documentation](https://docs.your-domain.com)
- [API Reference](docs/api/README.md)
- [FAQ](docs/FAQ.md)

### Commercial Support
For enterprise support, training, and custom development:
- Email: support@your-domain.com
- Website: https://your-domain.com

---

## 📈 Project Status

![GitHub Issues](https://img.shields.io/github/issues/your-org/maester-deployment)
![GitHub Pull Requests](https://img.shields.io/github/issues-pr/your-org/maester-deployment)
![GitHub Stars](https://img.shields.io/github/stars/your-org/maester-deployment)
![GitHub Forks](https://img.shields.io/github/forks/your-org/maester-deployment)

**Current Status**: Pre-Release (v0.9.0)  
**Stability**: Beta  
**Production Ready**: Use with caution, testing recommended

---

**Built with ❤️ for secure Microsoft 365 environments**

