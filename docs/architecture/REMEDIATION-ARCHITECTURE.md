# Remediation Architecture

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Status:** Pre-Release (v0.9.0)  
**Last Updated:** 2025-10-28

---

## Executive Summary

**The Maester Remediation Engine provides safe, auditable, and automated security configuration fixes with multi-level approval workflows and rollback capabilities.**

Key capabilities:
1. Three-tier remediation approach (Automated, Semi-Automated, Manual)
2. Built-in safety mechanisms with approval workflows
3. Complete audit trail and rollback capabilities
4. Integration with existing notification and ticketing systems

---

## 🎯 Remediation Overview

### Design Philosophy

**Safety First**: All remediations include:
- ✅ Pre-flight validation checks
- ✅ Dry-run capability (preview changes)
- ✅ Approval workflows for sensitive changes
- ✅ Automatic rollback on failure
- ✅ Complete audit logging
- ✅ Change impact analysis

### Remediation Tiers

```
┌─────────────────────────────────────────────────────────────┐
│                    Remediation Tiers                         │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Tier 1: Automated Remediation                              │
│  ├─ Low-risk changes                                         │
│  ├─ No approval required                                     │
│  ├─ Automatic rollback on failure                           │
│  └─ Examples: Enable MFA prompts, Update session timeouts   │
│                                                               │
│  Tier 2: Semi-Automated Remediation                         │
│  ├─ Medium-risk changes                                      │
│  ├─ Approval required (single approver)                     │
│  ├─ Preview changes before apply                            │
│  └─ Examples: Modify CA policies, Update security defaults  │
│                                                               │
│  Tier 3: Guided Manual Remediation                          │
│  ├─ High-risk or complex changes                            │
│  ├─ Step-by-step instructions provided                      │
│  ├─ Links to admin portals                                  │
│  └─ Examples: RBAC changes, Major policy overhauls          │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## 🏗️ Architecture Components

### Component Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                  Remediation Architecture                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌────────────────────────────────────────────────────────┐    │
│  │          Maester Test Execution                        │    │
│  │  ┌──────────────────────────────────────────────┐     │    │
│  │  │  Test Fails → Remediation Available?         │     │    │
│  │  └──────────────┬───────────────────────────────┘     │    │
│  └────────────────┼────────────────────────────────────────┘    │
│                    ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │        Remediation Engine (Orchestrator)               │    │
│  │  ┌──────────────────────────────────────────────┐     │    │
│  │  │  1. Load remediation definition              │     │    │
│  │  │  2. Validate prerequisites                   │     │    │
│  │  │  3. Check risk level                         │     │    │
│  │  │  4. Route to appropriate handler             │     │    │
│  │  └──────────────┬───────────────────────────────┘     │    │
│  └────────────────┼────────────────────────────────────────┘    │
│                    ↓                                              │
│         ┌──────────┴──────────┬──────────────────┐              │
│         ↓                      ↓                  ↓               │
│  ┌─────────────┐    ┌─────────────────┐   ┌──────────────┐    │
│  │ Automated   │    │ Semi-Automated  │   │   Guided     │    │
│  │ Remediation │    │  Remediation    │   │ Remediation  │    │
│  │             │    │                 │   │              │    │
│  │ • Execute   │    │ • Create ticket │   │ • Generate   │    │
│  │ • Validate  │    │ • Request       │   │   runbook    │    │
│  │ • Rollback  │    │   approval      │   │ • Send to    │    │
│  │   on error  │    │ • Execute on    │   │   admin      │    │
│  │             │    │   approval      │   │              │    │
│  └──────┬──────┘    └────────┬────────┘   └──────┬───────┘    │
│         │                    │                     │             │
│         └────────────────────┼─────────────────────┘             │
│                              ↓                                    │
│  ┌────────────────────────────────────────────────────────┐    │
│  │           Remediation Database                         │    │
│  │  • Remediation definitions                             │    │
│  │  • Execution history                                   │    │
│  │  • Approval records                                    │    │
│  │  • Rollback snapshots                                  │    │
│  └────────────────────────────────────────────────────────┘    │
│                              ↓                                    │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Microsoft Graph API                            │    │
│  │  • Apply configuration changes                         │    │
│  │  • Read current state                                  │    │
│  │  • Validate changes                                    │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📋 Remediation Definition Schema

### YAML Format

```yaml
remediation:
  id: MS.AAD.7.3v1-remediation
  test_id: MS.AAD.7.3v1
  name: Enable Phishing-Resistant MFA
  description: Configure Conditional Access to require phishing-resistant MFA methods
  
  metadata:
    category: Authentication
    risk_level: medium
    estimated_time: 5 minutes
    blast_radius: All users
    compliance_frameworks:
      - NIST-800-53: IA-2(1), IA-2(2)
      - ISO-27001: A.9.4.2
      - CMMC: AC.L2-3.1.12
    
  prerequisites:
    - name: Azure AD Premium P1 license
      type: license
      check: |
        Get-MgSubscribedSku | Where-Object {$_.SkuPartNumber -eq "AAD_PREMIUM"}
    - name: Conditional Access policies enabled
      type: feature
      check: |
        (Get-MgPolicyConditionalAccessPolicy).Count -gt 0
    - name: FIDO2 security keys configured
      type: configuration
      check: |
        Get-MgPolicyAuthenticationMethodPolicyAuthenticationMethodConfiguration -AuthenticationMethodId Fido2
  
  impact_analysis:
    affected_users: all_users
    affected_policies: ["Global MFA Policy"]
    breaking_changes: false
    user_impact: Users will need to register phishing-resistant MFA method
    
  remediation_steps:
    type: graph_api  # or powershell, arm_template, terraform
    
    dry_run: |
      # Preview changes without applying
      $policy = Get-MgIdentityConditionalAccessPolicy -Filter "displayName eq 'Global MFA Policy'"
      Write-Host "Will modify policy: $($policy.displayName)"
      Write-Host "Current MFA methods: $($policy.grantControls.authenticationStrength.displayName)"
      Write-Host "New MFA methods: Phishing-resistant"
      
    execute: |
      # Apply the remediation
      $policy = Get-MgIdentityConditionalAccessPolicy -Filter "displayName eq 'Global MFA Policy'"
      
      # Create or get authentication strength policy
      $authStrength = Get-MgPolicyAuthenticationStrengthPolicy -Filter "displayName eq 'Phishing-resistant MFA'"
      if (-not $authStrength) {
        $authStrength = New-MgPolicyAuthenticationStrengthPolicy -DisplayName "Phishing-resistant MFA" `
          -AllowedCombinations @("windowsHelloForBusiness", "fido2", "x509CertificateMultiFactor")
      }
      
      # Update conditional access policy
      Update-MgIdentityConditionalAccessPolicy -ConditionalAccessPolicyId $policy.Id -GrantControls @{
        AuthenticationStrength = @{
          Id = $authStrength.Id
        }
      }
      
      Write-Output "SUCCESS: Policy updated to require phishing-resistant MFA"
      
    validate: |
      # Verify the change was applied correctly
      $policy = Get-MgIdentityConditionalAccessPolicy -Filter "displayName eq 'Global MFA Policy'"
      $authStrength = Get-MgPolicyAuthenticationStrengthPolicy -AuthenticationStrengthPolicyId $policy.grantControls.authenticationStrength.id
      
      if ($authStrength.displayName -eq "Phishing-resistant MFA") {
        Write-Output "VALIDATED: Remediation successful"
        return $true
      } else {
        Write-Error "VALIDATION FAILED: Policy not updated correctly"
        return $false
      }
      
    rollback: |
      # Revert to previous state
      $policy = Get-MgIdentityConditionalAccessPolicy -Filter "displayName eq 'Global MFA Policy'"
      
      # Restore from snapshot
      $snapshot = Get-RemediationSnapshot -RemediationId $env:REMEDIATION_ID
      
      Update-MgIdentityConditionalAccessPolicy -ConditionalAccessPolicyId $policy.Id `
        -GrantControls $snapshot.OriginalGrantControls
      
      Write-Output "ROLLBACK: Reverted to previous configuration"
  
  approval:
    required: true
    approvers:
      - type: azure_ad_group
        id: "Security-Team-Approvers"
      - type: azure_ad_role
        role: "Security Administrator"
    timeout: 24h
    auto_approve_conditions:
      - condition: test_environment
        value: true
      - condition: dry_run
        value: true
        
  notifications:
    on_start:
      - email: security@company.com
      - teams: security-team-channel
    on_approval_required:
      - email: approvers@company.com
      - teams: approvals-channel
    on_success:
      - email: security@company.com
      - slack: security-notifications
    on_failure:
      - email: security@company.com
      - pagerduty: high_priority
      
  documentation:
    manual_steps: |
      If automated remediation fails, follow these manual steps:
      
      1. Navigate to Azure Portal > Azure Active Directory > Security > Conditional Access
      2. Select "Global MFA Policy"
      3. Under Grant controls, click "Require authentication strength"
      4. Select or create "Phishing-resistant MFA" policy
      5. Include: Windows Hello for Business, FIDO2 security keys, Certificate-based authentication
      6. Save and test with a test user
      
    references:
      - title: Microsoft Documentation - Authentication Strength
        url: https://learn.microsoft.com/entra/identity/authentication/concept-authentication-strengths
      - title: NIST 800-63B - Authentication Guidelines
        url: https://pages.nist.gov/800-63-3/sp800-63b.html
      - title: Maester Test Documentation
        url: https://maester.dev/docs/tests/MS.AAD.7.3v1
        
  tags:
    - mfa
    - conditional-access
    - phishing-resistant
    - zero-trust
```

---

## 🔄 Remediation Workflows

### Workflow 1: Automated Remediation (Tier 1)

```
Test Fails (Low Risk)
   ↓
Remediation Engine
   ↓
Load Remediation Definition
   ↓
Validate Prerequisites
   ├─ PASS → Continue
   └─ FAIL → Generate Manual Guide
      ↓
Create Snapshot (Backup)
   ↓
Execute Dry Run
   ↓
Review Dry Run Output
   ├─ Changes Acceptable → Continue
   └─ Issues Detected → Abort & Alert
      ↓
Execute Remediation
   ↓
Validate Changes
   ├─ VALID → Continue
   └─ INVALID → Rollback & Alert
      ↓
Update Compliance Status
   ↓
Send Success Notification
   ↓
Archive Audit Trail
```

**Timeline**: 2-5 minutes  
**User Interaction**: None required

---

### Workflow 2: Semi-Automated Remediation (Tier 2)

```
Test Fails (Medium Risk)
   ↓
Remediation Engine
   ↓
Load Remediation Definition
   ↓
Validate Prerequisites
   ↓
Execute Dry Run
   ↓
Generate Change Preview Report
   ├─ Impact Analysis
   ├─ Affected Users/Resources
   └─ Estimated Downtime
      ↓
Create Approval Request
   ├─ Send to Approvers
   │  ├─ Email with details
   │  ├─ Teams message
   │  └─ ServiceNow ticket
   └─ Set Timeout (24h default)
      ↓
   ┌──Wait for Approval──┐
   │                      │
   ↓                      ↓
Approved             Rejected/Timeout
   ↓                      ↓
Create Snapshot      Archive Request
   ↓                      ↓
Execute             Send Notification
Remediation              ↓
   ↓                   END
Validate
   ↓
Update Status
   ↓
Notify Stakeholders
   ↓
Archive Audit Trail
```

**Timeline**: 15 minutes to 24 hours  
**User Interaction**: Approval required

---

### Workflow 3: Guided Manual Remediation (Tier 3)

```
Test Fails (High Risk)
   ↓
Remediation Engine
   ↓
Load Remediation Definition
   ↓
Generate Remediation Runbook
   ├─ Step-by-step instructions
   ├─ Screenshots/diagrams
   ├─ Portal links
   ├─ PowerShell scripts
   └─ Validation checklist
      ↓
Create Tracking Ticket
   ├─ ServiceNow
   ├─ Jira
   └─ Azure DevOps
      ↓
Send to Administrator
   ├─ Email with runbook
   ├─ Teams notification
   └─ Slack message
      ↓
Administrator Executes
   ↓
Administrator Marks Complete
   ↓
Re-run Test to Validate
   ├─ PASS → Close Ticket
   └─ FAIL → Escalate
      ↓
Update Compliance Status
   ↓
Archive Documentation
```

**Timeline**: Hours to days  
**User Interaction**: Full manual execution

---

## 🛡️ Safety Mechanisms

### Pre-Flight Checks

```yaml
safety_checks:
  - name: Production Environment
    check: $env:ENVIRONMENT -eq 'production'
    action: require_approval
    
  - name: Blast Radius
    check: $affectedUsers -gt 1000
    action: require_senior_approval
    
  - name: Business Hours
    check: (Get-Date).Hour -ge 9 -and (Get-Date).Hour -le 17
    action: warn_out_of_hours
    
  - name: Change Freeze
    check: Test-ChangeFreeze
    action: block_remediation
    
  - name: Recent Failures
    check: (Get-RecentFailures -Hours 24).Count -gt 3
    action: require_investigation
```

### Rollback Capabilities

```powershell
# Automatic snapshot before any change
function New-RemediationSnapshot {
    param(
        [string]$RemediationId,
        [string]$ResourceType
    )
    
    $snapshot = @{
        Id = New-Guid
        RemediationId = $RemediationId
        Timestamp = Get-Date
        ResourceType = $ResourceType
        Configuration = Get-CurrentConfiguration -Type $ResourceType
        GraphApiCalls = @()  # Log all API calls
    }
    
    Save-Snapshot -Snapshot $snapshot
    return $snapshot.Id
}

# Rollback function
function Invoke-RemediationRollback {
    param(
        [string]$SnapshotId
    )
    
    $snapshot = Get-Snapshot -Id $SnapshotId
    
    # Replay configuration in reverse
    foreach ($apiCall in $snapshot.GraphApiCalls | Sort-Object -Descending) {
        Invoke-GraphApiRollback -Call $apiCall
    }
    
    # Validate rollback
    $current = Get-CurrentConfiguration -Type $snapshot.ResourceType
    if (Compare-Configuration $current $snapshot.Configuration) {
        Write-Output "Rollback successful"
    } else {
        Write-Error "Rollback validation failed - manual intervention required"
        Send-Alert -Severity Critical -Message "Rollback failed for $SnapshotId"
    }
}
```

---

## 📊 Remediation Database Schema

### PostgreSQL Schema

```sql
-- Remediation definitions
CREATE TABLE remediation_definitions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    test_id VARCHAR(100) NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    risk_level VARCHAR(20) CHECK (risk_level IN ('low', 'medium', 'high')),
    category VARCHAR(100),
    definition JSONB NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    created_by VARCHAR(255),
    version INTEGER DEFAULT 1,
    enabled BOOLEAN DEFAULT true,
    UNIQUE(test_id, version)
);

-- Remediation executions
CREATE TABLE remediation_executions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    remediation_id UUID REFERENCES remediation_definitions(id),
    test_execution_id UUID,
    status VARCHAR(50) CHECK (status IN ('pending', 'running', 'success', 'failed', 'rolled_back')),
    execution_type VARCHAR(20) CHECK (execution_type IN ('automated', 'semi_automated', 'manual')),
    started_at TIMESTAMP DEFAULT NOW(),
    completed_at TIMESTAMP,
    executed_by VARCHAR(255),
    dry_run BOOLEAN DEFAULT false,
    snapshot_id UUID,
    error_message TEXT,
    execution_log JSONB,
    CONSTRAINT execution_time_check CHECK (completed_at >= started_at)
);

-- Approval records
CREATE TABLE remediation_approvals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    execution_id UUID REFERENCES remediation_executions(id),
    approver_id VARCHAR(255) NOT NULL,
    approver_email VARCHAR(255),
    status VARCHAR(20) CHECK (status IN ('pending', 'approved', 'rejected', 'expired')),
    requested_at TIMESTAMP DEFAULT NOW(),
    responded_at TIMESTAMP,
    comments TEXT,
    approval_token VARCHAR(255) UNIQUE
);

-- Configuration snapshots
CREATE TABLE configuration_snapshots (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    execution_id UUID REFERENCES remediation_executions(id),
    resource_type VARCHAR(100),
    resource_id VARCHAR(255),
    configuration JSONB NOT NULL,
    graph_api_calls JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Remediation metrics
CREATE TABLE remediation_metrics (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    execution_id UUID REFERENCES remediation_executions(id),
    metric_name VARCHAR(100),
    metric_value NUMERIC,
    unit VARCHAR(50),
    recorded_at TIMESTAMP DEFAULT NOW()
);

-- Indexes for performance
CREATE INDEX idx_remediation_test_id ON remediation_definitions(test_id);
CREATE INDEX idx_execution_status ON remediation_executions(status);
CREATE INDEX idx_execution_started ON remediation_executions(started_at);
CREATE INDEX idx_approval_status ON remediation_approvals(status);
CREATE INDEX idx_approval_token ON remediation_approvals(approval_token);
```

---

## 🔌 Integration Points

### Ticketing System Integration

```yaml
ticketing_integrations:
  servicenow:
    enabled: true
    instance: company.service-now.com
    table: incident
    assignment_group: Security Operations
    category: Security Remediation
    priority_mapping:
      low: 4
      medium: 3
      high: 2
      
  jira:
    enabled: true
    project: SECURITY
    issue_type: Task
    labels: [maester, auto-remediation]
    
  azure_devops:
    enabled: true
    organization: company
    project: SecurityOps
    area_path: Security/Remediation
```

### Approval System Integration

```yaml
approval_integrations:
  email:
    enabled: true
    approval_link: true
    rejection_link: true
    
  teams:
    enabled: true
    adaptive_cards: true
    approval_buttons: true
    
  slack:
    enabled: true
    interactive_messages: true
    
  custom_webhook:
    enabled: true
    url: https://approval.company.com/api/v1/approvals
    headers:
      Authorization: Bearer ${APPROVAL_API_TOKEN}
```

---

## 📈 Metrics & Reporting

### Key Metrics

```yaml
remediation_metrics:
  - name: Mean Time To Remediate (MTTR)
    description: Average time from test failure to successful remediation
    target: < 24 hours
    
  - name: Remediation Success Rate
    description: Percentage of successful remediations
    target: > 95%
    
  - name: Automated Remediation Rate
    description: Percentage of issues fixed automatically
    target: > 60%
    
  - name: Rollback Rate
    description: Percentage of remediations requiring rollback
    target: < 5%
    
  - name: Approval Time
    description: Average time for approval decisions
    target: < 4 hours
```

### Reporting Dashboard

```
┌─────────────────────────────────────────────────────────┐
│           Remediation Dashboard                          │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  Total Remediations (30 days):        127               │
│  ├─ Automated:          76 (60%)                         │
│  ├─ Semi-Automated:     38 (30%)                         │
│  └─ Manual:             13 (10%)                         │
│                                                           │
│  Success Rate:                95.3%                      │
│  Average MTTR:                18.5 hours                 │
│  Rollback Rate:               2.4%                       │
│                                                           │
│  Pending Approvals:           5                          │
│  ├─ < 4 hours:         3                                 │
│  ├─ 4-12 hours:        1                                 │
│  └─ > 12 hours:        1 ⚠️                              │
│                                                           │
│  Top Remediated Issues:                                  │
│  1. Legacy auth enabled        : 23 times                │
│  2. MFA not required          : 18 times                │
│  3. Password expiration       : 15 times                │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

---

## 🔒 Security & Compliance

### Audit Trail

Every remediation action generates a comprehensive audit trail:

```json
{
  "audit_id": "550e8400-e29b-41d4-a716-446655440000",
  "remediation_id": "MS.AAD.7.3v1-remediation",
  "execution_id": "660e8400-e29b-41d4-a716-446655440001",
  "timestamp": "2025-10-28T14:32:15Z",
  "actor": {
    "type": "automated",
    "service_principal": "maester-remediation-sp",
    "initiator": "scheduled_test_run"
  },
  "actions": [
    {
      "sequence": 1,
      "action": "snapshot_created",
      "timestamp": "2025-10-28T14:32:16Z",
      "details": {
        "snapshot_id": "770e8400-e29b-41d4-a716-446655440002",
        "resources": ["ConditionalAccessPolicy/abc123"]
      }
    },
    {
      "sequence": 2,
      "action": "graph_api_call",
      "timestamp": "2025-10-28T14:32:18Z",
      "details": {
        "method": "PATCH",
        "endpoint": "/v1.0/identity/conditionalAccess/policies/abc123",
        "request_body": {...},
        "response_code": 200
      }
    },
    {
      "sequence": 3,
      "action": "validation_successful",
      "timestamp": "2025-10-28T14:32:20Z"
    }
  ],
  "result": "success",
  "affected_resources": [
    "Global MFA Policy (abc123)"
  ],
  "affected_users": "all_users",
  "compliance_frameworks": ["NIST-800-53", "ISO-27001"]
}
```

### Compliance Evidence

```yaml
compliance_evidence:
  auto_generate: true
  include:
    - before_state: Configuration snapshot before remediation
    - after_state: Configuration snapshot after remediation
    - execution_log: Complete execution log
    - approval_record: Approval decision and approver
    - validation_result: Post-remediation validation
    - audit_trail: Complete audit trail
  format: [json, pdf, html]
  retention: 7_years
  encryption: true
```

---

## 📚 Example Remediations

### Example 1: Simple Automated Remediation

**Test**: Legacy authentication protocols enabled  
**Risk Level**: Low  
**Type**: Automated

```yaml
remediation:
  id: MS.AAD.2.1v1-remediation
  name: Disable Legacy Authentication
  risk_level: low
  
  execute: |
    # Block legacy auth protocols
    $policy = Get-MgPolicyAuthenticationMethodPolicy
    $policy.LegacyAuthEnabled = $false
    Update-MgPolicyAuthenticationMethodPolicy -Policy $policy
```

### Example 2: Complex Semi-Automated Remediation

**Test**: Guest user access too permissive  
**Risk Level**: Medium  
**Type**: Semi-Automated

```yaml
remediation:
  id: MS.AAD.4.2v1-remediation
  name: Restrict Guest User Access
  risk_level: medium
  approval: required
  
  execute: |
    # Update external collaboration settings
    $settings = Get-MgPolicyAuthorizationPolicy
    $settings.GuestUserRoleId = "10dae51f-b6af-4016-8d66-8c2a99b929b3"  # Restricted Guest
    $settings.AllowInvitesFrom = "adminsAndGuestInviters"
    Update-MgPolicyAuthorizationPolicy -Settings $settings
```

### Example 3: Guided Manual Remediation

**Test**: Privileged roles without MFA  
**Risk Level**: High  
**Type**: Manual (guided)

```yaml
remediation:
  id: MS.AAD.8.1v1-remediation
  name: Enforce MFA for Privileged Roles
  risk_level: high
  
  manual_steps: |
    1. Navigate to Azure Portal > Azure AD > Security > Conditional Access
    2. Create new policy: "Require MFA for Admins"
    3. Under Assignments > Users and Groups:
       - Include: Directory roles
       - Select: Global Administrator, Security Administrator, etc.
    4. Under Access Controls > Grant:
       - Select "Require multi-factor authentication"
    5. Enable policy and test with admin test account
    6. Monitor sign-in logs for 24 hours
```

---

## 🚀 Deployment

### Container Updates

Add remediation engine to deployment:

```yaml
# kubernetes/base/remediation-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: maester-remediation
spec:
  replicas: 2
  template:
    spec:
      containers:
      - name: remediation-engine
        image: maester/remediation-engine:v0.9.0
        env:
        - name: REMEDIATION_MODE
          value: "semi_automated"
        - name: APPROVAL_REQUIRED
          value: "true"
```

### Database Migration

```sql
-- Run migrations for remediation tables
psql -h $DB_HOST -U maester -d maester_db -f migrations/001_remediation_schema.sql
```

---

## 📖 Next Steps

1. Review [Remediation Development Guide](../development/remediation-development.md)
2. See [Remediation API Reference](../api/remediation-api.md)
3. Read [Safety & Testing Guidelines](remediation-safety.md)
4. Explore [Example Remediations](../examples/remediations/)

---

**Document Status**: Draft  
**Review Required**: Yes  
**Target Release**: v1.0.0

