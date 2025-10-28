# ADR-001: Workload Identity Federation for Authentication

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Date:** 2025-10-28  
**Status:** Accepted

---

## Context

The Maester Deployment Framework needs to authenticate with Microsoft Graph API to execute security tests against Microsoft 365 tenants. Traditional approaches involve storing application secrets or certificates, which introduces security risks and operational overhead.

### Options Considered

1. **Service Principal with Client Secret**
2. **Service Principal with Certificate**
3. **Managed Identity** (Azure only)
4. **Workload Identity Federation** (Recommended)

---

## Decision

We will use **Workload Identity Federation** as the primary authentication method for all supported platforms (vSphere, Azure, AWS, GCP).

---

## Rationale

### Security Benefits

**No Secrets Storage:**
- Eliminates the risk of secret exposure in configuration files
- No secrets in environment variables or Kubernetes secrets
- Reduces attack surface significantly

**Automatic Token Rotation:**
- Tokens are short-lived (typically 1 hour)
- No manual rotation required
- Reduces risk of token compromise

**Native Cloud Integration:**
- Leverages cloud-native identity systems
- OIDC-based trust relationship
- Industry-standard authentication flow

### Operational Benefits

**Simplified Management:**
- No secret rotation workflows
- No certificate expiration tracking
- Reduced operational burden

**Platform Agnostic:**
- Works across all major cloud providers
- Consistent authentication pattern
- Portable across environments

**Audit Trail:**
- Clear identity attribution
- Enhanced logging and monitoring
- Compliance-friendly

---

## Implementation

### Azure Kubernetes Service (AKS)

```yaml
# ServiceAccount with workload identity
apiVersion: v1
kind: ServiceAccount
metadata:
  name: maester-runner
  annotations:
    azure.workload.identity/client-id: <AZURE_CLIENT_ID>
```

### AWS EKS (IRSA - IAM Roles for Service Accounts)

```yaml
# ServiceAccount with IAM role annotation
apiVersion: v1
kind: ServiceAccount
metadata:
  name: maester-runner
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::ACCOUNT:role/maester-role
```

### Google Cloud GKE

```yaml
# ServiceAccount with Workload Identity
apiVersion: v1
kind: ServiceAccount
metadata:
  name: maester-runner
  annotations:
    iam.gke.io/gcp-service-account: maester@PROJECT.iam.gserviceaccount.com
```

### vSphere with Vanilla Kubernetes

Requires manual OIDC provider setup but follows the same pattern.

---

## Consequences

### Positive

- ✅ Enhanced security posture
- ✅ Reduced operational complexity
- ✅ Consistent authentication across platforms
- ✅ Improved audit capabilities
- ✅ No secret management overhead
- ✅ Cloud-native best practices

### Negative

- ⚠️ Initial setup complexity (one-time)
- ⚠️ Requires OIDC provider configuration
- ⚠️ Platform-specific implementation details
- ⚠️ Troubleshooting requires understanding of OIDC flow

### Neutral

- 📌 Not supported on all Kubernetes versions (requires 1.20+)
- 📌 Fallback to service principal available for legacy systems
- 📌 Documentation burden for setup process

---

## Alternatives Considered

### Service Principal with Client Secret

**Pros:**
- Simple to implement
- Well-documented
- Works everywhere

**Cons:**
- Security risk (secret exposure)
- Requires rotation
- Secret management complexity

**Decision:** Rejected due to security concerns

### Service Principal with Certificate

**Pros:**
- More secure than client secret
- Longer validity period

**Cons:**
- Certificate management complexity
- Still requires secure storage
- Expiration tracking needed

**Decision:** Rejected in favor of Workload Identity

### Managed Identity (Azure only)

**Pros:**
- Azure-native solution
- No secrets required
- Simple implementation

**Cons:**
- Azure-only (not portable)
- Limited to Azure resources

**Decision:** Supported as Azure-specific option, but Workload Identity preferred for consistency

---

## Migration Path

For organizations currently using service principals:

1. **Phase 1**: Deploy with Workload Identity in dev/test
2. **Phase 2**: Validate functionality
3. **Phase 3**: Migrate production
4. **Phase 4**: Deprecate service principal (v2.0.0)

---

## References

- [Azure Workload Identity](https://azure.github.io/azure-workload-identity/)
- [AWS IRSA](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html)
- [GCP Workload Identity](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity)
- [OIDC Specification](https://openid.net/connect/)

---

## Review Schedule

This ADR will be reviewed:
- After v1.0.0 release
- If new authentication methods become available
- If security vulnerabilities are discovered

