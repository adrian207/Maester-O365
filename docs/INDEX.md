# Maester Deployment Framework - Documentation Index

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Version:** 0.9.0  
**Last Updated:** 2025-10-28

---

## 📚 Documentation Overview

This is the complete documentation index for the Maester Deployment Framework v0.9.0 sub-1.0 release.

---

## 🚀 Quick Links

| Document | Description | Audience |
|----------|-------------|----------|
| [README](../README.md) | Project overview and features | Everyone |
| [GETTING-STARTED](GETTING-STARTED.md) | Quick start guide (30 min) | New users |
| [ARCHITECTURE](ARCHITECTURE.md) | System architecture | Architects, DevOps |
| [CONTRIBUTING](../CONTRIBUTING.md) | How to contribute | Contributors |
| [CHANGELOG](../CHANGELOG.md) | Version history | Everyone |

---

## 📖 Documentation Structure

### Core Documentation

```
maester-deployment/
├── README.md                          # Project overview
├── GETTING-STARTED.md                 # Quick start guide
├── CONTRIBUTING.md                    # Contribution guidelines
├── CODE_OF_CONDUCT.md                 # Community standards
├── LICENSE                            # MIT License
├── SECURITY.md                        # Security policy
├── CHANGELOG.md                       # Version history
├── ROADMAP.md                         # Product roadmap
│
└── docs/
    ├── INDEX.md                       # This file
    ├── ARCHITECTURE.md                # System architecture
    ├── GETTING-STARTED.md             # Beginner guide
    │
    ├── architecture/                  # Architecture docs
    │   ├── REMEDIATION-ARCHITECTURE.md
    │   ├── SERVERLESS-ARCHITECTURE.md
    │   └── adr/                       # Architecture Decision Records
    │       ├── 001-workload-identity-federation.md
    │       ├── 002-three-tier-remediation.md
    │       └── 003-vsphere-as-primary-platform.md
    │
    ├── deployment-guides/             # Platform-specific guides
    │   ├── docker-compose.md
    │   ├── vsphere-tanzu.md
    │   ├── vsphere-vanilla.md
    │   ├── vsphere-rke2.md
    │   ├── azure-serverless.md
    │   ├── aws-serverless.md
    │   ├── azure-aks.md
    │   ├── aws-eks.md
    │   └── gcp-gke.md
    │
    ├── compliance/                    # Compliance frameworks
    │   ├── README.md
    │   └── frameworks/
    │       ├── NIST-800-53.md
    │       ├── CIS-Benchmarks.md
    │       ├── ISO-27001.md
    │       ├── HIPAA.md
    │       ├── PCI-DSS.md
    │       ├── SOC2.md
    │       └── CMMC.md
    │
    ├── operations/                    # Operations guides
    │   ├── configuration.md
    │   ├── monitoring.md
    │   ├── backup-recovery.md
    │   ├── troubleshooting.md
    │   ├── updates.md
    │   └── security-best-practices.md
    │
    ├── development/                   # Development docs
    │   ├── custom-tests.md
    │   ├── remediation-development.md
    │   └── testing.md
    │
    └── api/                          # API documentation
        ├── README.md
        ├── compliance-api.md
        ├── remediation-api.md
        └── notification-api.md
```

---

## 🎯 Documentation by Role

### 👔 Executive / Decision Maker

**Goal:** Understand value proposition and ROI

1. [README - Executive Summary](../README.md#overview)
2. [Compliance Frameworks](compliance/README.md)
3. [ROI Calculator](operations/roi-calculator.md)
4. [Success Stories](case-studies/README.md)

**Time Required:** 15-20 minutes

---

### 🏗️ Architect / Technical Lead

**Goal:** Understand architecture and design decisions

1. [System Architecture](ARCHITECTURE.md)
2. [Deployment Models](ARCHITECTURE.md#deployment-models)
3. [Remediation Architecture](architecture/REMEDIATION-ARCHITECTURE.md)
4. [Serverless Architecture](architecture/SERVERLESS-ARCHITECTURE.md)
5. [ADRs](architecture/adr/)
   - [Workload Identity Federation](architecture/adr/001-workload-identity-federation.md)
   - [Three-Tier Remediation](architecture/adr/002-three-tier-remediation.md)
   - [vSphere Primary Platform](architecture/adr/003-vsphere-as-primary-platform.md)

**Time Required:** 2-3 hours

---

### 🔧 DevOps / Platform Engineer

**Goal:** Deploy and operate the framework

1. [Getting Started](GETTING-STARTED.md)
2. Choose deployment guide:
   - [Docker Compose](deployment-guides/docker-compose.md)
   - [vSphere Tanzu](deployment-guides/vsphere-tanzu.md)
   - [Azure Serverless](deployment-guides/azure-serverless.md)
3. [Configuration Reference](operations/configuration.md)
4. [Monitoring & Alerting](operations/monitoring.md)
5. [Backup & Recovery](operations/backup-recovery.md)
6. [Troubleshooting](operations/troubleshooting.md)

**Time Required:** 4-8 hours (includes deployment)

---

### 🛡️ Security Engineer / Analyst

**Goal:** Configure security tests and compliance mapping

1. [Compliance Framework Overview](compliance/README.md)
2. Select frameworks:
   - [NIST 800-53](compliance/frameworks/NIST-800-53.md)
   - [ISO 27001](compliance/frameworks/ISO-27001.md)
   - [HIPAA](compliance/frameworks/HIPAA.md)
3. [Custom Test Development](development/custom-tests.md)
4. [Remediation Configuration](architecture/REMEDIATION-ARCHITECTURE.md)
5. [Security Best Practices](operations/security-best-practices.md)

**Time Required:** 3-5 hours

---

### 👨‍💻 Developer / Contributor

**Goal:** Contribute code or custom tests

1. [Contributing Guide](../CONTRIBUTING.md)
2. [Development Setup](../CONTRIBUTING.md#development-setup)
3. [Custom Tests](development/custom-tests.md)
4. [Remediation Development](development/remediation-development.md)
5. [API Documentation](api/README.md)
6. [Testing Guide](development/testing.md)

**Time Required:** 2-4 hours

---

### 📋 Compliance Officer / Auditor

**Goal:** Generate compliance reports and evidence

1. [Compliance Overview](compliance/README.md)
2. [Framework Mappings](compliance/README.md#supported-compliance-frameworks)
3. [Evidence Collection](compliance/README.md#evidence-automation)
4. [Compliance API](api/compliance-api.md)
5. [Report Templates](operations/reporting.md)

**Time Required:** 1-2 hours

---

## 📊 Documentation by Task

### Initial Deployment

1. ✅ [Prerequisites Check](GETTING-STARTED.md#prerequisites)
2. ✅ [Azure AD Setup](GETTING-STARTED.md#step-1-azure-ad-setup)
3. ✅ [Choose Platform](ARCHITECTURE.md#deployment-models)
4. ✅ [Deploy](deployment-guides/)
5. ✅ [Verify Installation](operations/verification.md)

---

### Configuration

1. ✅ [Environment Variables](operations/configuration.md#environment-variables)
2. ✅ [Test Selection](operations/configuration.md#test-configuration)
3. ✅ [Notification Setup](operations/configuration.md#notifications)
4. ✅ [Storage Configuration](operations/configuration.md#storage)
5. ✅ [Compliance Mapping](operations/configuration.md#compliance)

---

### Operations

1. ✅ [Daily Operations](operations/daily-operations.md)
2. ✅ [Monitoring](operations/monitoring.md)
3. ✅ [Backup & Recovery](operations/backup-recovery.md)
4. ✅ [Updates & Maintenance](operations/updates.md)
5. ✅ [Troubleshooting](operations/troubleshooting.md)

---

### Development

1. ✅ [Custom Tests](development/custom-tests.md)
2. ✅ [Remediation Functions](development/remediation-development.md)
3. ✅ [API Integration](api/README.md)
4. ✅ [Testing](development/testing.md)
5. ✅ [Contributing](../CONTRIBUTING.md)

---

## 🎓 Learning Paths

### Beginner Track (4-6 hours)

```
1. Read README (15 min)
   ↓
2. Complete Getting Started (1 hour)
   ↓
3. Review Architecture Overview (30 min)
   ↓
4. Deploy Docker Compose (30 min)
   ↓
5. Configure Notifications (30 min)
   ↓
6. Review First Report (30 min)
   ↓
7. Read Operations Guide (1 hour)
```

### Intermediate Track (8-12 hours)

```
Beginner Track
   ↓
1. Deep-dive Architecture (2 hours)
   ↓
2. Deploy to Production Platform (4 hours)
   ↓
3. Configure Compliance Mapping (2 hours)
   ↓
4. Setup Monitoring (2 hours)
   ↓
5. Create Custom Tests (2 hours)
```

### Advanced Track (20+ hours)

```
Intermediate Track
   ↓
1. Study Remediation Architecture (3 hours)
   ↓
2. Implement Custom Remediations (4 hours)
   ↓
3. Multi-Platform Deployment (4 hours)
   ↓
4. API Integration Development (4 hours)
   ↓
5. Contribute to Project (5+ hours)
```

---

## 🔍 Documentation by Topic

### Authentication & Security
- [Workload Identity Federation ADR](architecture/adr/001-workload-identity-federation.md)
- [Security Best Practices](operations/security-best-practices.md)
- [SECURITY.md](../SECURITY.md)

### Remediation
- [Remediation Architecture](architecture/REMEDIATION-ARCHITECTURE.md)
- [Three-Tier Remediation ADR](architecture/adr/002-three-tier-remediation.md)
- [Remediation Development](development/remediation-development.md)

### Compliance
- [Compliance Overview](compliance/README.md)
- [Framework Mappings](compliance/frameworks/)
- [Compliance API](api/compliance-api.md)

### Deployment
- [Deployment Overview](ARCHITECTURE.md#deployment-models)
- [vSphere Deployment](deployment-guides/vsphere-tanzu.md)
- [Serverless Deployment](architecture/SERVERLESS-ARCHITECTURE.md)

### Operations
- [Configuration](operations/configuration.md)
- [Monitoring](operations/monitoring.md)
- [Troubleshooting](operations/troubleshooting.md)

---

## 📝 Release Notes

### Current Release: v0.9.0 (2025-10-28)

**Status:** Pre-Release  
**Stability:** Beta  
**Production Ready:** Use with caution

**What's New:**
- Initial pre-release with core functionality
- Multi-platform deployment support
- Remediation engine with three tiers
- 7 compliance framework mappings
- Comprehensive documentation

[Full Changelog](../CHANGELOG.md)

---

## 🗺️ Roadmap

See [ROADMAP.md](../ROADMAP.md) for:
- Upcoming features
- Release timeline
- Community requests
- Long-term vision

**Next Release:** v1.0.0 (Target: February 2026)

---

## 🤝 Contributing

We welcome contributions!

- [Contributing Guide](../CONTRIBUTING.md)
- [Code of Conduct](../CODE_OF_CONDUCT.md)
- [GitHub Issues](https://github.com/your-org/maester-deployment/issues)
- [GitHub Discussions](https://github.com/your-org/maester-deployment/discussions)

---

## 💡 Getting Help

### Documentation

If you can't find what you're looking for:
1. Use search function (Ctrl+F / Cmd+F)
2. Check [FAQ](FAQ.md)
3. Review [Troubleshooting Guide](operations/troubleshooting.md)

### Community Support

- [GitHub Discussions](https://github.com/your-org/maester-deployment/discussions)
- [Discord Community](https://discord.gg/maester)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/maester)

### Commercial Support

For enterprise support:
- Email: support@your-domain.com
- Website: https://your-domain.com/support

---

## 📧 Contact

**Project Author:** Adrian Johnson <adrian207@gmail.com>

**Security Issues:** security@your-domain.com

**General Inquiries:** info@your-domain.com

---

## ⭐ Quick Reference

### Most Popular Pages

1. [Getting Started](GETTING-STARTED.md) - Start here
2. [Docker Compose Deployment](deployment-guides/docker-compose.md) - Fastest deployment
3. [vSphere Tanzu Deployment](deployment-guides/vsphere-tanzu.md) - Enterprise deployment
4. [Remediation Architecture](architecture/REMEDIATION-ARCHITECTURE.md) - Automated fixes
5. [Compliance Frameworks](compliance/README.md) - Compliance mapping

### External Resources

- **Maester Project:** https://maester.dev
- **Microsoft Graph:** https://docs.microsoft.com/graph
- **Pester:** https://pester.dev
- **VMware vSphere:** https://docs.vmware.com/en/VMware-vSphere/

---

**Last Updated:** 2025-10-28  
**Documentation Version:** 0.9.0  
**Next Review:** Before v1.0.0 release

---

*This documentation is continuously improved. Found an issue or have a suggestion? [Create an issue](https://github.com/your-org/maester-deployment/issues) or [submit a pull request](https://github.com/your-org/maester-deployment/pulls).*

