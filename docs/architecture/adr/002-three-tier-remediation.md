# ADR-002: Three-Tier Remediation Architecture

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Date:** 2025-10-28  
**Status:** Accepted

---

## Context

The Maester Deployment Framework detects security configuration issues but needs a safe, auditable way to fix them. Organizations have varying risk tolerance and different requirements for change approval and automation.

### Problem Statement

How do we enable automated remediation while maintaining:
- Safety and control
- Audit trails
- Approval workflows
- Rollback capabilities
- Risk management

---

## Decision

We will implement a **three-tier remediation architecture**:

1. **Tier 1: Automated Remediation** - Low-risk, immediate execution
2. **Tier 2: Semi-Automated Remediation** - Medium-risk, approval required
3. **Tier 3: Guided Manual Remediation** - High-risk, step-by-step instructions

---

## Rationale

### Tier 1: Automated Remediation

**Use Cases:**
- Enabling MFA prompts
- Updating session timeouts
- Disabling legacy authentication (non-breaking)
- Security default adjustments

**Characteristics:**
- Low blast radius
- No breaking changes
- Automatic rollback on failure
- No approval required

**Safety Mechanisms:**
- Pre-flight validation
- Configuration snapshots
- Automatic rollback
- Complete audit trail

**Example:**
```yaml
remediation:
  tier: automated
  risk_level: low
  approval_required: false
  automatic_rollback: true
```

### Tier 2: Semi-Automated Remediation

**Use Cases:**
- Modifying Conditional Access policies
- Updating authentication methods
- Changing security defaults
- Role assignment modifications

**Characteristics:**
- Medium blast radius
- Potential user impact
- Approval workflow required
- Preview changes before apply

**Safety Mechanisms:**
- All Tier 1 mechanisms
- Approval workflow
- Dry-run capability
- Impact analysis
- Scheduled execution

**Example:**
```yaml
remediation:
  tier: semi_automated
  risk_level: medium
  approval_required: true
  approvers:
    - security-team-approvers
  timeout: 24h
```

### Tier 3: Guided Manual Remediation

**Use Cases:**
- Major RBAC changes
- Complex multi-step configurations
- Policy overhauls
- Architecture changes

**Characteristics:**
- High blast radius
- Significant user impact
- Manual execution required
- Expert review needed

**Guidance Provided:**
- Step-by-step runbook
- Portal links
- PowerShell scripts (for copy-paste)
- Validation checklist
- Rollback procedures

**Example:**
```yaml
remediation:
  tier: manual
  risk_level: high
  documentation: |
    Step-by-step instructions...
  references:
    - Microsoft documentation links
    - Internal procedures
```

---

## Implementation

### Remediation Engine Architecture

```
Test Failure
   ↓
Load Remediation Definition
   ↓
Risk Assessment
   ↓
   ├─→ Tier 1: Execute immediately
   ├─→ Tier 2: Create approval request
   └─→ Tier 3: Generate runbook
```

### Database Schema

```sql
CREATE TABLE remediations (
    id UUID PRIMARY KEY,
    tier VARCHAR(20),  -- automated, semi_automated, manual
    risk_level VARCHAR(20),  -- low, medium, high
    approval_required BOOLEAN,
    ...
);
```

---

## Decision Matrix

### Tier Assignment Criteria

| Criteria | Tier 1 | Tier 2 | Tier 3 |
|----------|--------|--------|--------|
| **Blast Radius** | < 100 users | 100-1000 users | > 1000 users |
| **Breaking Change** | No | Maybe | Yes |
| **User Impact** | Minimal | Moderate | Significant |
| **Rollback Time** | < 1 min | < 15 min | > 15 min |
| **Complexity** | Simple | Moderate | Complex |
| **Risk Score** | 0-30 | 31-70 | 71-100 |

### Risk Score Calculation

```
Risk Score = (Blast Radius × 0.4) + (User Impact × 0.3) + (Complexity × 0.2) + (Rollback Difficulty × 0.1)

Where each factor is scored 0-100
```

---

## Consequences

### Positive

- ✅ Balances automation with safety
- ✅ Accommodates different risk profiles
- ✅ Provides flexibility for organizations
- ✅ Clear escalation path
- ✅ Comprehensive audit trail
- ✅ Gradual automation adoption

### Negative

- ⚠️ Complexity in tier assignment
- ⚠️ Approval workflow overhead
- ⚠️ Initial configuration required
- ⚠️ Training requirements

### Neutral

- 📌 Organizations can customize tier assignments
- 📌 Can promote Tier 2 to Tier 1 over time
- 📌 Manual tier available as safety net

---

## Alternatives Considered

### Fully Automated Approach

**Concept:** Automate everything without tiers

**Pros:**
- Simplest to implement
- Fastest remediation

**Cons:**
- Too risky for most organizations
- No approval workflow
- Limited control

**Decision:** Rejected - too aggressive for production use

### Manual-Only Approach

**Concept:** Provide only guided instructions

**Pros:**
- Safest approach
- Full human control

**Cons:**
- Slow remediation
- Human error risk
- Doesn't leverage automation

**Decision:** Rejected - doesn't provide enough automation value

### Two-Tier Approach

**Concept:** Only automated and manual tiers

**Pros:**
- Simpler than three tiers
- Clear decision point

**Cons:**
- Missing middle ground
- Forces too many to manual
- Less flexible

**Decision:** Rejected - semi-automated tier provides crucial middle ground

---

## Migration Path

Organizations can adopt progressively:

**Phase 1: Observation**
- Run all in dry-run mode
- Review proposed changes
- Build confidence

**Phase 2: Guided Manual**
- Start with Tier 3
- Execute with guidance
- Validate results

**Phase 3: Semi-Automated**
- Enable Tier 2 remediations
- Approve changes
- Monitor results

**Phase 4: Automated**
- Enable Tier 1 remediations
- Full automation for low-risk
- Continuous improvement

---

## Success Metrics

**Adoption:**
- % of organizations using each tier
- Time to first remediation
- Tier progression timeline

**Effectiveness:**
- Mean Time To Remediate (MTTR)
- Remediation success rate
- Rollback rate by tier

**Safety:**
- Incidents caused by remediation
- False positive remediation rate
- Approval rejection rate

**Target Metrics:**
- MTTR < 24 hours (Tier 2)
- Success rate > 95%
- Rollback rate < 5%
- Incident rate < 0.1%

---

## References

- [Site Reliability Engineering](https://sre.google/books/)
- [ITIL Change Management](https://www.axelos.com/certifications/itil-service-management)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)

---

## Review Schedule

This ADR will be reviewed:
- After 6 months of production use
- After gathering community feedback
- If remediation-related incidents occur
- Before v2.0.0 release

