# Contributing to Maester Deployment Framework

**Author:** Adrian Johnson <adrian207@gmail.com>

Thank you for your interest in contributing to the Maester Deployment Framework! This document provides guidelines and instructions for contributing to the project.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Contribution Workflow](#contribution-workflow)
- [Coding Standards](#coding-standards)
- [Testing Requirements](#testing-requirements)
- [Documentation](#documentation)
- [Pull Request Process](#pull-request-process)
- [Community](#community)

---

## Code of Conduct

This project adheres to a Code of Conduct that all contributors are expected to follow. Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) before contributing.

---

## How Can I Contribute?

### Reporting Bugs

**Before submitting a bug report:**
- Check the [existing issues](https://github.com/your-org/maester-deployment/issues) to avoid duplicates
- Collect relevant information (logs, configuration, environment details)
- Verify the bug exists in the latest version

**When submitting a bug report, include:**
- Clear and descriptive title
- Steps to reproduce the issue
- Expected vs actual behavior
- Environment details (OS, deployment platform, versions)
- Relevant logs or screenshots
- Possible solutions (if you have ideas)

**Use the bug report template** when creating an issue.

### Suggesting Enhancements

We welcome feature requests and enhancement suggestions!

**Before submitting:**
- Check existing issues and discussions
- Consider if the feature fits the project scope
- Think about how it benefits the broader community

**When suggesting enhancements:**
- Use a clear and descriptive title
- Provide detailed use case and rationale
- Include examples or mockups if applicable
- Consider implementation approaches
- Note any potential breaking changes

**Use the feature request template** when creating an issue.

### Contributing Code

We accept contributions in several areas:

**1. Core Framework**
- Docker containers and images
- Kubernetes manifests and Helm charts
- Terraform modules
- Serverless implementations

**2. Remediation Functions**
- New automated remediations
- Remediation improvements
- Safety checks and validations

**3. Compliance Mappings**
- New framework mappings
- Control mapping updates
- Evidence collection improvements

**4. Documentation**
- Deployment guides
- API documentation
- Tutorials and examples
- Translations

**5. Tests**
- Custom Maester tests
- Integration tests
- End-to-end tests

---

## Development Setup

### Prerequisites

**Required:**
- Git
- Docker Desktop 20.10+ or Podman
- Visual Studio Code (recommended) or your preferred editor
- PowerShell 7.4+
- Terraform 1.5+ (for infrastructure changes)
- Kubectl (for Kubernetes changes)

**Optional:**
- Azure CLI (for Azure deployments)
- AWS CLI (for AWS deployments)
- Google Cloud SDK (for GCP deployments)

### Clone the Repository

```bash
git clone https://github.com/your-org/maester-deployment.git
cd maester-deployment
```

### Development Environment Setup

```bash
# Install development dependencies
./scripts/setup/dev-setup.sh

# Verify setup
./scripts/test/verify-setup.sh
```

### Local Development

```bash
# Run local Docker Compose environment
docker-compose -f docker-compose.dev.yml up -d

# Access local environment
# Report Server: http://localhost:8080
# API: http://localhost:8080/api
```

---

## Contribution Workflow

### 1. Create a Branch

```bash
# Update main branch
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/your-feature-name

# Or for bug fixes
git checkout -b fix/bug-description
```

### Branch Naming Conventions

- `feature/` - New features or enhancements
- `fix/` - Bug fixes
- `docs/` - Documentation changes
- `refactor/` - Code refactoring
- `test/` - Test additions or modifications
- `chore/` - Maintenance tasks

### 2. Make Changes

- Follow coding standards (see below)
- Write clear commit messages
- Add/update tests as needed
- Update documentation

### 3. Commit Changes

**Commit Message Format:**

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Test additions/changes
- `chore`: Maintenance tasks

**Example:**

```bash
git commit -m "feat(remediation): add automated guest access restriction

Implements automated remediation for MS.AAD.4.2v1 test.
Includes dry-run capability and approval workflow.

Closes #123"
```

### 4. Push and Create Pull Request

```bash
# Push branch
git push origin feature/your-feature-name

# Create pull request on GitHub
# Use the pull request template
```

---

## Coding Standards

### PowerShell

**Style Guide:**
- Follow [PowerShell Practice and Style Guide](https://poshcode.gitbook.io/powershell-practice-and-style/)
- Use PascalCase for function names
- Use meaningful variable names
- Include comment-based help for functions
- Use approved verbs (`Get-Verb`)

**Example:**

```powershell
<#
.SYNOPSIS
    Retrieves compliance score for a specific framework.

.DESCRIPTION
    Calculates and returns the compliance score based on test results
    mapped to the specified framework controls.

.PARAMETER Framework
    The compliance framework identifier (e.g., "nist-800-53-r5")

.PARAMETER TestResults
    The test results object from Maester execution

.EXAMPLE
    Get-ComplianceScore -Framework "nist-800-53-r5" -TestResults $results

.NOTES
    Author: Adrian Johnson <adrian207@gmail.com>
#>
function Get-ComplianceScore {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory = $true)]
        [string]$Framework,
        
        [Parameter(Mandatory = $true)]
        [object]$TestResults
    )
    
    # Implementation
}
```

### Python

**Style Guide:**
- Follow [PEP 8](https://pep8.org/)
- Use type hints
- Include docstrings
- Use meaningful variable names

**Example:**

```python
"""
Compliance mapping module for Maester framework.

Author: Adrian Johnson <adrian207@gmail.com>
"""

from typing import Dict, List, Optional
from dataclasses import dataclass

@dataclass
class ComplianceControl:
    """Represents a single compliance control."""
    id: str
    title: str
    description: str
    framework: str
    
def map_test_to_controls(
    test_id: str,
    framework: str
) -> List[ComplianceControl]:
    """
    Map a Maester test to compliance controls.
    
    Args:
        test_id: The Maester test identifier
        framework: The compliance framework identifier
        
    Returns:
        List of compliance controls mapped to the test
        
    Raises:
        ValueError: If framework is not supported
    """
    # Implementation
    pass
```

### YAML/JSON

- Use 2-space indentation
- Follow alphabetical ordering for keys (where logical)
- Include comments for complex configurations
- Validate syntax before committing

### Terraform

**Style Guide:**
- Follow [Terraform Style Guide](https://www.terraform.io/docs/language/syntax/style.html)
- Use meaningful resource names
- Include descriptions for variables
- Use modules for reusability

**Example:**

```hcl
variable "resource_group_name" {
  description = "Name of the Azure resource group for Maester deployment"
  type        = string
  
  validation {
    condition     = length(var.resource_group_name) > 0
    error_message = "Resource group name cannot be empty."
  }
}
```

---

## Testing Requirements

### Required Tests

All code contributions must include appropriate tests:

**1. Unit Tests**
- Test individual functions/components
- Mock external dependencies
- Aim for >80% code coverage

**2. Integration Tests**
- Test component interactions
- Use test environment
- Verify end-to-end workflows

**3. Infrastructure Tests**
- Terraform validation
- Kubernetes manifest validation
- Security scanning

### Running Tests

```bash
# Run all tests
./scripts/test/run-tests.sh

# Run specific test suites
./scripts/test/run-unit-tests.sh
./scripts/test/run-integration-tests.sh
./scripts/test/run-infrastructure-tests.sh

# Run linters
./scripts/test/run-linters.sh
```

### Test Coverage

```bash
# Generate coverage report
./scripts/test/coverage-report.sh

# View coverage in browser
open coverage/index.html
```

---

## Documentation

### Documentation Requirements

All contributions should include relevant documentation:

**Code Documentation:**
- Inline comments for complex logic
- Function/module documentation
- README files for new components

**User Documentation:**
- Deployment guides for new features
- Configuration examples
- Troubleshooting tips

**API Documentation:**
- Endpoint descriptions
- Request/response examples
- Authentication requirements

### Documentation Style

- Use clear, concise language
- Include code examples
- Add diagrams where helpful
- Follow existing documentation structure
- Update table of contents

### Building Documentation Locally

```bash
# Build documentation site
cd docs
./build-docs.sh

# Serve locally
./serve-docs.sh

# Access at http://localhost:3000
```

---

## Pull Request Process

### Before Submitting

**Checklist:**
- [ ] Code follows project style guidelines
- [ ] All tests pass locally
- [ ] New tests added for new functionality
- [ ] Documentation updated
- [ ] Commit messages follow convention
- [ ] No merge conflicts with main branch
- [ ] Self-review completed
- [ ] Breaking changes documented

### Pull Request Template

Use the PR template provided. Include:

**Title Format:**
```
<type>: <brief description>
```

**Description:**
- Summary of changes
- Related issues (Fixes #123, Closes #456)
- Testing performed
- Screenshots (if applicable)
- Breaking changes (if any)
- Checklist completion

### Review Process

1. **Automated Checks**: CI/CD pipeline runs automatically
   - Linting
   - Unit tests
   - Integration tests
   - Security scans

2. **Code Review**: Maintainers review your code
   - Typically 1-2 reviewers required
   - Address feedback promptly
   - Discussion and iteration encouraged

3. **Approval**: Once approved and checks pass
   - Maintainer will merge
   - Squash and merge for feature branches
   - Standard merge for hotfixes

### After Merge

- Delete your feature branch
- Update local main branch
- Close related issues (if not auto-closed)

---

## Code Review Guidelines

### For Contributors

**Responding to Feedback:**
- Be open to suggestions
- Ask questions if unclear
- Make requested changes promptly
- Mark conversations as resolved after addressing

**Best Practices:**
- Keep PRs focused and reasonably sized
- Respond to reviews within 2-3 business days
- Be respectful and professional
- Learn from feedback

### For Reviewers

**Review Focus:**
- Code correctness and logic
- Test coverage
- Documentation completeness
- Security implications
- Performance considerations
- Style compliance

**Providing Feedback:**
- Be constructive and specific
- Explain the "why" behind suggestions
- Distinguish between required changes and suggestions
- Approve when satisfied, even with minor nitpicks

---

## Development Guidelines

### Security Considerations

- Never commit secrets or credentials
- Use environment variables or secret management
- Follow least privilege principle
- Validate all inputs
- Sanitize outputs
- Keep dependencies updated

### Performance Guidelines

- Optimize for common use cases
- Consider scalability implications
- Profile performance-critical code
- Document performance characteristics
- Add performance tests for critical paths

### Accessibility

- Follow WCAG guidelines for UI components
- Include alt text for images
- Ensure keyboard navigation
- Test with screen readers

---

## Release Process

### Versioning

We follow [Semantic Versioning](https://semver.org/):

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

### Release Cycle

- **Patch releases**: As needed for bug fixes
- **Minor releases**: Monthly (feature releases)
- **Major releases**: Annually or for breaking changes

---

## Community

### Getting Help

- **GitHub Discussions**: For questions and discussions
- **GitHub Issues**: For bug reports and feature requests
- **Discord**: Real-time community chat
- **Email**: For private matters: adrian207@gmail.com

### Recognition

Contributors are recognized in:
- CONTRIBUTORS.md file
- Release notes
- Project README
- Annual contributor highlight

---

## License

By contributing to this project, you agree that your contributions will be licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## Questions?

If you have questions about contributing:
- Check existing documentation
- Search closed issues
- Ask in GitHub Discussions
- Contact maintainers

Thank you for contributing to Maester Deployment Framework! 🚀

