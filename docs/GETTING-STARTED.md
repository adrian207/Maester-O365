# Getting Started with Maester Deployment Framework

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Version:** 0.9.0

Welcome to the Maester Deployment Framework! This guide will help you get your first deployment running in less than an hour.

---

## 🎯 Overview

The Maester Deployment Framework automates Microsoft 365 security testing using [Maester](https://maester.dev), provides compliance mapping to major frameworks, and enables automated remediation of security issues.

**What you'll achieve:**
- Automated daily security scans of your M365 tenant
- Real-time compliance scoring for NIST, ISO 27001, HIPAA, and more
- Interactive HTML reports with detailed findings
- Optional automated remediation of common issues

---

## 📋 Prerequisites

### Required

1. **Microsoft 365 Tenant**
   - Azure AD Premium P1 or P2
   - Global Administrator or Security Administrator role

2. **Deployment Platform** (choose one):
   - Docker + Docker Compose
   - Kubernetes cluster (any distribution)
   - vSphere 7.0+ environment
   - Azure/AWS/GCP account (for serverless)

3. **Basic Skills**:
   - Command line familiarity
   - Basic understanding of containers or Kubernetes
   - Microsoft 365 administration knowledge

### Optional
- Git for version control
- Terraform for infrastructure automation
- Basic PowerShell knowledge

---

## 🚀 Quick Start (Docker Compose)

The fastest way to get started is with Docker Compose. This takes about 30 minutes.

### Step 1: Azure AD Setup

Create an App Registration in Azure AD:

```bash
# Using Azure CLI
az login

# Create App Registration
az ad app create \
  --display-name "Maester Deployment Framework" \
  --sign-in-audience AzureADMyOrg

# Note the Application (client) ID
CLIENT_ID=$(az ad app list --display-name "Maester Deployment Framework" --query "[0].appId" -o tsv)
echo "Client ID: $CLIENT_ID"

# Create service principal
az ad sp create --id $CLIENT_ID

# Assign Microsoft Graph permissions
az ad app permission add \
  --id $CLIENT_ID \
  --api 00000003-0000-0000-c000-000000000000 \
  --api-permissions \
    7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role \
    246dd0d5-5bd0-4def-940b-0421030a5b68=Role \
    5b567255-7703-4780-807c-7be8301ae99b=Role \
    483bed4a-2ad3-4361-a73b-c83ccdbdc53c=Role

# Grant admin consent (requires Global Administrator)
az ad app permission admin-consent --id $CLIENT_ID

echo "✅ Azure AD setup complete!"
```

### Step 2: Clone Repository

```bash
git clone https://github.com/your-org/maester-deployment.git
cd maester-deployment
```

### Step 3: Configure Environment

```bash
# Copy example environment file
cp .env.example .env

# Edit configuration
nano .env
```

Update these values in `.env`:

```bash
# Azure AD Configuration
AZURE_TENANT_ID=your-tenant-id
AZURE_CLIENT_ID=your-client-id
AZURE_CLIENT_SECRET=your-client-secret  # Or use Managed Identity

# Test Configuration
TEST_SCHEDULE="0 2 * * *"  # Daily at 2 AM
TEST_TAGS="EIDSCA,CA,MFA"
REPORT_RETENTION_DAYS=90

# Notification Configuration (optional)
NOTIFICATION_EMAIL=security@yourcompany.com
TEAMS_WEBHOOK_URL=https://...
SLACK_WEBHOOK_URL=https://...

# Storage Configuration
CLOUD_STORAGE_PROVIDER=azure  # or aws, gcp
STORAGE_ACCOUNT_NAME=maesterreports
```

### Step 4: Deploy

```bash
# Start all services
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f
```

### Step 5: Run First Test

```bash
# Trigger manual test run
docker-compose exec maester-runner pwsh -Command "Invoke-MaesterTests"

# Or wait for scheduled run at 2 AM
```

### Step 6: View Results

Access the web interface:

```bash
# Open browser
open http://localhost:8080

# Or get the URL
echo "Report Server: http://$(hostname):8080"
```

**Default credentials:**
- Username: `admin`
- Password: (check `.env` file)

---

## 🎉 Success! What's Next?

Your Maester Deployment Framework is now running! Here's what to do next:

### Immediate Next Steps

1. **Review Your First Report**
   - Navigate to http://localhost:8080
   - Review failed tests
   - Check compliance scores

2. **Configure Notifications**
   - Set up email alerts
   - Configure Teams or Slack webhooks
   - Test notification channels

3. **Customize Tests**
   - Review `tests/` directory
   - Add custom tests for your organization
   - Configure test schedules

### Within First Week

1. **Enable Remediation** (optional)
   - Review remediation documentation
   - Start with Tier 3 (manual guidance)
   - Gradually enable automated remediations

2. **Set Up Monitoring**
   - Configure Prometheus metrics
   - Import Grafana dashboards
   - Set up alerts

3. **Backup Configuration**
   - Document your configuration
   - Set up automated backups
   - Test restore procedures

### Within First Month

1. **Production Deployment**
   - Move to production environment
   - Implement high availability
   - Configure disaster recovery

2. **Team Training**
   - Train security team
   - Document runbooks
   - Establish processes

3. **Compliance Integration**
   - Map tests to your frameworks
   - Generate compliance reports
   - Schedule audit preparation

---

## 🏗️ Other Deployment Options

### vSphere Kubernetes

For enterprise on-premise deployments:

```bash
# See detailed guide
open docs/deployment-guides/vsphere-tanzu.md
```

**Estimated Time:** 4-6 hours  
**Prerequisites:** vSphere 7.0+, vCenter access

### Azure Serverless

For cloud-native, cost-effective deployments:

```bash
# See detailed guide
open docs/deployment-guides/azure-serverless.md
```

**Estimated Time:** 1-2 hours  
**Monthly Cost:** $35-90

### Full Kubernetes

For maximum scalability:

```bash
# See detailed guide
open docs/deployment-guides/kubernetes.md
```

**Estimated Time:** 2-4 hours  
**Prerequisites:** Existing K8s cluster

---

## 📚 Learning Path

### Beginner

1. ✅ Complete Quick Start (this guide)
2. Read [Architecture Overview](docs/ARCHITECTURE.md)
3. Review [Configuration Guide](docs/operations/configuration.md)
4. Watch [Video Tutorials](https://www.youtube.com/maester)

### Intermediate

1. Review [Remediation Architecture](docs/architecture/REMEDIATION-ARCHITECTURE.md)
2. Study [Compliance Mappings](docs/compliance/README.md)
3. Customize [Notification Templates](docs/operations/notifications.md)
4. Create [Custom Tests](docs/development/custom-tests.md)

### Advanced

1. Deploy [Multi-Cloud Setup](docs/deployment-guides/multi-cloud.md)
2. Implement [GitOps Workflows](docs/operations/gitops.md)
3. Contribute [New Features](CONTRIBUTING.md)
4. Join [Community Calls](https://maester.dev/community)

---

## 🔧 Troubleshooting

### Common Issues

**Issue:** Can't connect to Microsoft Graph

```bash
# Check authentication
docker-compose exec maester-runner pwsh -Command "Connect-MgGraph -ClientId $CLIENT_ID -TenantId $TENANT_ID"

# Verify permissions
az ad app permission list --id $CLIENT_ID
```

**Issue:** Tests not running on schedule

```bash
# Check cron configuration
docker-compose logs maester-runner | grep -i cron

# Manual test
docker-compose exec maester-runner pwsh -File /app/run-tests.ps1
```

**Issue:** Can't access web interface

```bash
# Check if services are running
docker-compose ps

# Check logs
docker-compose logs report-server

# Verify port binding
netstat -an | grep 8080
```

### Get Help

- 📖 [Troubleshooting Guide](docs/operations/troubleshooting.md)
- 💬 [GitHub Discussions](https://github.com/your-org/maester-deployment/discussions)
- 🐛 [Report Issues](https://github.com/your-org/maester-deployment/issues)
- 📧 Email: adrian207@gmail.com

---

## 📖 Documentation

### Essential Reading
- [Architecture Overview](docs/ARCHITECTURE.md)
- [Configuration Reference](docs/operations/configuration.md)
- [Deployment Guides](docs/deployment-guides/)
- [Operations Guide](docs/operations/README.md)

### Deep Dives
- [Remediation Architecture](docs/architecture/REMEDIATION-ARCHITECTURE.md)
- [Serverless Architecture](docs/architecture/SERVERLESS-ARCHITECTURE.md)
- [Compliance Frameworks](docs/compliance/README.md)
- [API Documentation](docs/api/README.md)

---

## 🤝 Community

Join our community:

- **Discord:** https://discord.gg/maester
- **Twitter:** [@MaesterFramework](https://twitter.com/MaesterFramework)
- **GitHub:** https://github.com/your-org/maester-deployment
- **Blog:** https://blog.maester.dev

---

## ✅ Checklist

Use this checklist to track your progress:

- [ ] Azure AD App Registration created
- [ ] Permissions granted and consented
- [ ] Environment configured (`.env` file)
- [ ] Docker Compose deployed
- [ ] First test run completed
- [ ] Web interface accessible
- [ ] Compliance report reviewed
- [ ] Notifications configured
- [ ] Backups configured
- [ ] Team trained
- [ ] Documentation reviewed

---

## 🎓 Next Steps

Now that you're up and running:

1. **Explore Features:**
   - [Automated Remediation](docs/architecture/REMEDIATION-ARCHITECTURE.md)
   - [Compliance Mapping](docs/compliance/README.md)
   - [Custom Tests](docs/development/custom-tests.md)

2. **Join the Community:**
   - Share your experience
   - Ask questions
   - Contribute improvements

3. **Plan Production:**
   - Review [Security Best Practices](docs/operations/security-best-practices.md)
   - Plan [High Availability](docs/operations/high-availability.md)
   - Set up [Monitoring](docs/operations/monitoring.md)

---

**Congratulations! You've successfully deployed the Maester Deployment Framework! 🎉**

For questions or feedback: adrian207@gmail.com

