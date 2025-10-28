# Compliance Framework Documentation

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Version:** 0.9.0  
**Last Updated:** 2025-10-28

---

## Overview

The Maester Deployment Framework provides comprehensive compliance mapping and automated evidence collection for multiple security and regulatory frameworks. This document outlines the supported frameworks, mapping methodology, and evidence automation capabilities.

---

## Supported Compliance Frameworks

| Framework | Version | Controls | Coverage | Evidence | Status |
|-----------|---------|----------|----------|----------|--------|
| [NIST 800-53](frameworks/NIST-800-53.md) | Revision 5 | 1,194 total<br/>342 applicable | 84% automated | ✅ Automated | Production |
| [CIS Benchmarks](frameworks/CIS-Benchmarks.md) | M365 v3.0 | 150+ | 100% automated | ✅ Automated | Production |
| [ISO 27001](frameworks/ISO-27001.md) | 2022 | 93 (Annex A) | 100% automated | ✅ Automated | Production |
| [HIPAA](frameworks/HIPAA.md) | Security Rule | 45 standards | 100% automated | ✅ Automated | Production |
| [PCI-DSS](frameworks/PCI-DSS.md) | v4.0 | 12 requirements | Applicable controls | ✅ Automated | Production |
| [SOC 2](frameworks/SOC2.md) | Type II | 5 Trust Services | 100% automated | ✅ Automated | Production |
| [CMMC](frameworks/CMMC.md) | 2.0 | Level 1-3 | 100% automated | ✅ Automated | Production |
| [FedRAMP](frameworks/FedRAMP.md) | Moderate/High | Based on NIST 800-53 | 84% automated | ✅ Automated | Beta |
| [HITRUST CSF](frameworks/HITRUST.md) | v11 | 156 control families | 75% automated | ✅ Automated | Beta |
| [GDPR](frameworks/GDPR.md) | Current | Technical controls | Partial | ✅ Automated | Beta |

---

## Compliance Mapping Methodology

### Mapping Approach

Each Maester test is mapped to one or more compliance controls using a structured YAML format:

```yaml
# Example: MS.AAD.7.3v1 - Phishing-Resistant MFA
test_id: MS.AAD.7.3v1
name: Phishing-Resistant MFA Required
description: Conditional Access requires phishing-resistant authentication methods

compliance_mappings:
  nist_800_53_r5:
    - control: IA-2(1)
      family: Identification and Authentication
      title: Multi-Factor Authentication to Privileged Accounts
      implementation: Automated test validates phishing-resistant MFA enforcement
      coverage: Full
      
    - control: IA-2(2)
      family: Identification and Authentication
      title: Multi-Factor Authentication to Non-Privileged Accounts
      implementation: Validates MFA for all user accounts
      coverage: Full
      
  iso_27001_2022:
    - control: A.9.4.2
      title: Secure log-on procedures
      implementation: Validates strong authentication mechanisms
      coverage: Full
      
    - control: A.9.4.3
      title: Password management system
      implementation: Validates passwordless authentication options
      coverage: Partial
      
  cmmc_2_0:
    - control: AC.L2-3.1.12
      level: 2
      practice: Control remote access sessions
      implementation: Validates MFA for remote access
      coverage: Full
      
  hipaa:
    - control: 164.312(a)(2)(i)
      standard: Access Control - Unique User Identification
      implementation: Validates unique authentication factors
      coverage: Full
      
  pci_dss_v4:
    - requirement: 8.3.1
      title: Multi-factor authentication for all remote access
      implementation: Validates MFA enforcement
      coverage: Full

evidence_collection:
  automated: true
  artifacts:
    - type: conditional_access_policy
      format: json
      description: Complete CA policy configuration
    - type: authentication_methods
      format: json
      description: Enabled authentication methods
    - type: test_result
      format: json
      description: Pass/fail result with timestamp
  retention: 7_years
  encryption: true
```

---

## Compliance Scoring

### Score Calculation

```
Compliance Score = (Passed Tests / Total Applicable Tests) × 100

Where:
- Passed Tests: Number of tests that passed
- Total Applicable Tests: Number of tests mapped to framework controls
```

### Score Interpretation

| Score Range | Status | Action Required |
|-------------|--------|-----------------|
| 95-100% | ✅ Excellent | Maintain current posture |
| 85-94% | 🟢 Good | Minor improvements needed |
| 75-84% | 🟡 Fair | Moderate improvements needed |
| 60-74% | 🟠 Poor | Significant improvements needed |
| 0-59% | 🔴 Critical | Immediate action required |

---

## Compliance Reports

### Report Types

#### 1. Executive Summary Report

**Purpose**: High-level overview for leadership  
**Format**: PDF, PowerPoint  
**Contents**:
- Overall compliance score by framework
- Trend analysis (3-12 months)
- Top risk areas
- Remediation roadmap
- Executive recommendations

**Example**:
```
COMPLIANCE EXECUTIVE SUMMARY
Period: October 2025

Framework Scores:
├─ NIST 800-53 Rev 5:     92.3% 🟢 (+2.1% vs last month)
├─ ISO 27001:2022:        94.8% ✅ (+0.5% vs last month)
├─ HIPAA Security Rule:   88.7% 🟢 (-1.2% vs last month)
├─ PCI-DSS v4.0:          96.1% ✅ (+3.2% vs last month)
└─ SOC 2 Type II:         91.5% 🟢 (No change)

Top 3 Risk Areas:
1. Legacy authentication protocols still enabled
2. Guest user access permissions too broad
3. Privileged role members without MFA

Remediation Estimate: 15 days with 2 FTE
```

#### 2. Detailed Compliance Report

**Purpose**: Control-by-control analysis  
**Format**: Excel, PDF, HTML  
**Contents**:
- Complete control list
- Test mapping details
- Pass/fail status
- Evidence references
- Remediation guidance

#### 3. Gap Analysis Report

**Purpose**: Identify compliance gaps  
**Format**: Excel, PDF  
**Contents**:
- Failed controls
- Risk assessment per gap
- Remediation steps
- Resource requirements
- Timeline estimates

#### 4. Evidence Package

**Purpose**: Audit-ready evidence  
**Format**: ZIP archive  
**Contents**:
- Test results (JSON/CSV)
- Configuration exports
- Screenshots
- Timestamps and audit trails
- Digital signatures
- Chain of custody documentation

---

## Framework-Specific Details

### NIST 800-53 Revision 5

**Total Controls**: 1,194  
**Applicable to M365**: 342  
**Automated Coverage**: 287 (84%)  
**Manual Processes**: 55 (16%)

**Control Families**:
- Access Control (AC): 32 controls
- Audit and Accountability (AU): 18 controls
- Identification and Authentication (IA): 15 controls
- System and Communications Protection (SC): 24 controls
- [Full list...](frameworks/NIST-800-53.md)

**Key Features**:
- Automated control assessment
- Continuous monitoring
- Evidence auto-collection
- OSCAL format support

---

### CIS Microsoft 365 Benchmarks

**Total Recommendations**: 150+  
**Coverage**: 100%  
**Level 1**: Basic security (all automated)  
**Level 2**: Enhanced security (all automated)

**Recommendation Categories**:
1. Account / Authentication (25 recommendations)
2. Application Permissions (12 recommendations)
3. Data Management (18 recommendations)
4. Email Security (22 recommendations)
5. Auditing (15 recommendations)

---

### ISO 27001:2022

**Annex A Controls**: 93  
**Coverage**: 100% automated  
**Domains**: 4 main themes

**Control Themes**:
1. Organizational controls (37 controls)
2. People controls (8 controls)
3. Physical controls (14 controls)
4. Technological controls (34 controls)

**M365-Relevant Controls**: 45 controls

---

### HIPAA Security Rule

**Standards**: 45  
**Coverage**: 100% of technical safeguards  
**Focus**: Protected Health Information (PHI)

**Safeguard Categories**:
1. **Administrative Safeguards** (9 standards)
   - Security Management Process
   - Workforce Security
   - Information Access Management

2. **Physical Safeguards** (4 standards)
   - Facility Access Controls
   - Workstation Security
   - Device and Media Controls

3. **Technical Safeguards** (5 standards)
   - Access Control
   - Audit Controls
   - Integrity
   - Person or Entity Authentication
   - Transmission Security

---

### PCI-DSS v4.0

**Requirements**: 12  
**Applicable Controls**: M365 identity and access controls  
**Coverage**: All applicable requirements

**Relevant Requirements**:
- Requirement 8: Identify users and authenticate access
- Requirement 10: Log and monitor all access
- Requirement 12: Support information security with policies

---

### SOC 2 Type II

**Trust Service Criteria**: 5  
**Coverage**: 100%  
**Report Type**: Type II (operational effectiveness)

**Trust Services**:
1. **Security** (C1): 100% coverage
2. **Availability** (A1): Partial coverage
3. **Processing Integrity** (PI1): Partial coverage
4. **Confidentiality** (C2): 90% coverage
5. **Privacy** (P1): 85% coverage

---

### CMMC 2.0

**Levels**: 3  
**Coverage**: Full for Level 1-3  
**Focus**: Defense supply chain

**Level Breakdown**:
- **Level 1** (Foundational): 17 practices - 100% automated
- **Level 2** (Advanced): 110 practices - 85% automated
- **Level 3** (Expert): 130 practices - 75% automated

---

## Evidence Automation

### Automated Evidence Collection

**Evidence Types**:
1. **Configuration Exports**
   - Conditional Access policies (JSON)
   - Authentication methods (JSON)
   - Security defaults (JSON)
   - Role assignments (JSON)

2. **Test Results**
   - Pass/fail status
   - Execution timestamp
   - Detailed findings
   - Remediation guidance

3. **Audit Logs**
   - Sign-in logs
   - Audit logs
   - Risk detections
   - Policy changes

4. **Screenshots**
   - Policy configurations
   - Admin portal views
   - Test execution results

### Evidence Package Structure

```
evidence-package-2025-10-28/
├── metadata.json
├── executive-summary.pdf
├── compliance-scorecard.pdf
├── test-results/
│   ├── all-results.json
│   ├── all-results.csv
│   └── all-results.html
├── configurations/
│   ├── conditional-access-policies.json
│   ├── authentication-methods.json
│   ├── security-defaults.json
│   └── role-assignments.json
├── screenshots/
│   ├── policy-01.png
│   ├── policy-02.png
│   └── ...
├── audit-logs/
│   ├── sign-in-logs-30days.json
│   ├── audit-logs-30days.json
│   └── risk-detections.json
├── mappings/
│   ├── nist-800-53-mapping.xlsx
│   ├── iso-27001-mapping.xlsx
│   └── ...
├── attestations/
│   ├── control-attestation-form.pdf
│   └── executive-attestation.pdf
└── signatures/
    ├── digital-signature.p7s
    └── chain-of-custody.pdf
```

---

## Compliance Dashboard

### Real-Time Compliance Monitoring

**Dashboard Features**:
- Live compliance scores
- Trend charts (6-12 months)
- Control heatmaps
- Risk indicators
- Remediation tracking
- Audit timeline

**Dashboard Sections**:

```
┌────────────────────────────────────────────────────────────┐
│            COMPLIANCE DASHBOARD                             │
├────────────────────────────────────────────────────────────┤
│                                                              │
│  Overall Compliance Score: 92.8% 🟢 (+1.2% vs last month)  │
│                                                              │
│  Framework Scores:                                          │
│  ████████████████░░  NIST 800-53      92.3%                │
│  ██████████████████  ISO 27001        94.8%                │
│  ████████████████░░  HIPAA            88.7%                │
│  ███████████████████ PCI-DSS          96.1%                │
│  █████████████████░  SOC 2            91.5%                │
│                                                              │
│  Recent Activity:                                           │
│  ├─ 3 controls fixed (Oct 25)                              │
│  ├─ 2 new gaps identified (Oct 27)                         │
│  └─ 1 remediation in progress                              │
│                                                              │
│  Upcoming Audits:                                           │
│  ├─ SOC 2 Type II: Nov 15, 2025                            │
│  └─ HIPAA Assessment: Dec 1, 2025                          │
│                                                              │
└────────────────────────────────────────────────────────────┘
```

---

## API Access

### Compliance API Endpoints

```http
# Get compliance score for a framework
GET /api/v1/compliance/{framework}/score

# Get control details
GET /api/v1/compliance/{framework}/controls/{control_id}

# Get evidence package
GET /api/v1/compliance/{framework}/evidence?from=YYYY-MM-DD&to=YYYY-MM-DD

# Get gap analysis
GET /api/v1/compliance/{framework}/gaps

# Get trend data
GET /api/v1/compliance/{framework}/trends?period=6months
```

**Example Response**:

```json
{
  "framework": "nist-800-53-r5",
  "score": 92.3,
  "timestamp": "2025-10-28T14:30:00Z",
  "summary": {
    "total_controls": 342,
    "applicable_controls": 287,
    "passed_controls": 265,
    "failed_controls": 22,
    "not_tested": 0
  },
  "trend": {
    "last_month": 90.2,
    "change_percent": 2.1,
    "direction": "improving"
  },
  "top_gaps": [
    {
      "control": "IA-2(1)",
      "title": "Multi-Factor Authentication to Privileged Accounts",
      "status": "failed",
      "risk": "high"
    }
  ]
}
```

---

## Compliance Workflow

### Continuous Compliance Process

```
┌─────────────────────────────────────────────────────────┐
│        Continuous Compliance Workflow                    │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  Daily:                                                   │
│    ↓                                                      │
│  Run Maester Tests                                       │
│    ↓                                                      │
│  Map Results to Frameworks                               │
│    ↓                                                      │
│  Calculate Compliance Scores                             │
│    ↓                                                      │
│  Detect Changes/Drift                                    │
│    ├─→ No Changes: Archive results                      │
│    └─→ Changes Detected                                 │
│         ↓                                                 │
│       Alert Security Team                                │
│         ↓                                                 │
│       Evaluate Impact                                    │
│         ├─→ Minor: Log and monitor                      │
│         └─→ Significant                                  │
│              ↓                                            │
│            Trigger Remediation                           │
│              ↓                                            │
│            Re-test After Fix                             │
│              ↓                                            │
│            Update Compliance Score                       │
│              ↓                                            │
│            Generate Evidence                             │
│                                                           │
│  Monthly:                                                 │
│    ↓                                                      │
│  Generate Compliance Reports                             │
│    ↓                                                      │
│  Executive Review Meeting                                │
│    ↓                                                      │
│  Update Risk Register                                    │
│                                                           │
│  Quarterly:                                               │
│    ↓                                                      │
│  Compliance Framework Review                             │
│    ↓                                                      │
│  Update Control Mappings                                 │
│    ↓                                                      │
│  Audit Preparation                                       │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

---

## Custom Framework Support

### Adding Custom Frameworks

Organizations can add custom compliance frameworks:

```yaml
# custom-frameworks/company-security-standard.yaml
framework:
  id: company-security-standard
  name: Company Security Standard
  version: v2.0
  effective_date: 2025-01-01
  
controls:
  - id: CSS-IAM-001
    title: Multi-Factor Authentication Required
    description: All users must use MFA for authentication
    priority: P0
    mapped_tests:
      - MS.AAD.7.3v1
      - MS.AAD.7.4v1
    
  - id: CSS-IAM-002
    title: Privileged Access Review
    description: Privileged roles reviewed quarterly
    priority: P1
    mapped_tests:
      - MS.AAD.3.1v1
      - MS.AAD.3.2v1
```

---

## Compliance Resources

### Framework Documentation
- [NIST 800-53](frameworks/NIST-800-53.md)
- [CIS Benchmarks](frameworks/CIS-Benchmarks.md)
- [ISO 27001](frameworks/ISO-27001.md)
- [HIPAA](frameworks/HIPAA.md)
- [PCI-DSS](frameworks/PCI-DSS.md)
- [SOC 2](frameworks/SOC2.md)
- [CMMC](frameworks/CMMC.md)

### Mapping Files
- Located in `compliance/frameworks/` directory
- YAML format for easy customization
- Version controlled in Git

### API Documentation
- [Compliance API Reference](../api/compliance-api.md)

---

## Support

For questions about compliance mapping or framework support:
- GitHub Issues: [Report issues](https://github.com/your-org/maester-deployment/issues)
- Email: compliance-support@your-domain.com
- Documentation: [Compliance Docs](https://docs.your-domain.com/compliance)

---

**Document Version**: 0.9.0  
**Last Review**: 2025-10-28  
**Next Review**: Before v1.0.0 release

