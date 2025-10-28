# Git Release Instructions for v0.9.0

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Date:** 2025-10-28

---

## 📋 Pre-Release Checklist

Before pushing to GitHub, verify:

- [ ] All documentation files created
- [ ] No sensitive data in any files
- [ ] All placeholder URLs updated
- [ ] Version numbers consistent (v0.9.0)
- [ ] Author information correct
- [ ] License file present

---

## 🚀 Step-by-Step Release Instructions

### Step 1: Initialize Git Repository (if not already done)

```bash
# Navigate to project directory
cd c:\Github\Maester-O365

# Initialize git repository
git init

# Check current status
git status
```

### Step 2: Create .gitignore

```bash
# Create .gitignore file
cat > .gitignore << 'EOF'
# Environment files
.env
.env.local
.env.*.local

# Secrets
secrets/
*.key
*.pem
*.pfx

# Logs
logs/
*.log

# OS files
.DS_Store
Thumbs.db
desktop.ini

# IDE files
.vscode/
.idea/
*.swp
*.swo
*~

# Build outputs
dist/
build/
*.zip
*.tar.gz

# Node modules
node_modules/

# Python
__pycache__/
*.py[cod]
*$py.class
.venv/
venv/

# Terraform
*.tfstate
*.tfstate.*
.terraform/
.terraform.lock.hcl
crash.log

# Docker
.dockerignore

# Test artifacts
test-results/
coverage/
*.coveragerc
EOF
```

### Step 3: Stage All Documentation Files

```bash
# Add all files to staging
git add .

# Verify what's staged
git status

# Review changes
git diff --cached
```

### Step 4: Create Initial Commit

```bash
# Commit with descriptive message
git commit -m "feat: Initial v0.9.0 release - Maester Deployment Framework

- Complete documentation suite (300+ pages)
- Multi-platform deployment support (vSphere, Azure, AWS, GCP, Docker)
- Remediation engine with three-tier approach
- Compliance mapping for 10 frameworks (NIST, ISO, HIPAA, etc.)
- Serverless architecture documentation
- Architecture Decision Records (ADRs)
- GitHub templates and CI/CD workflows
- Comprehensive getting started guides

BREAKING CHANGE: Initial release

Closes #1"
```

### Step 5: Create GitHub Repository

**Option A: Via GitHub Web Interface**

1. Go to https://github.com/new
2. Repository name: `Maester-O365` or `maester-deployment`
3. Description: "Enterprise deployment framework for Maester - Microsoft 365 security automation"
4. Visibility: Public (or Private for internal use)
5. DO NOT initialize with README (we have one)
6. Click "Create repository"

**Option B: Via GitHub CLI**

```bash
# Install GitHub CLI if needed
# https://cli.github.com/

# Login to GitHub
gh auth login

# Create repository
gh repo create Maester-O365 \
  --public \
  --description "Enterprise deployment framework for Maester - Microsoft 365 security automation" \
  --homepage "https://maester.dev"
```

### Step 6: Add Remote and Push

```bash
# Add GitHub remote (replace with your username/org)
git remote add origin https://github.com/YOUR-USERNAME/Maester-O365.git

# Verify remote
git remote -v

# Rename branch to main (if needed)
git branch -M main

# Push to GitHub
git push -u origin main
```

### Step 7: Create Release Tag

```bash
# Create annotated tag for v0.9.0
git tag -a v0.9.0 -m "Release v0.9.0 - Pre-Release

Maester Deployment Framework v0.9.0

Features:
- Multi-platform deployments (vSphere, Azure, AWS, GCP, Docker)
- Remediation engine (3-tier approach)
- Compliance mapping (10 frameworks)
- Serverless architectures
- Workload Identity Federation
- Complete documentation (300+ pages)

This is a pre-release version (sub-1.0). Use with caution in production.

See RELEASE-SUMMARY-v0.9.0.md for complete details."

# Push tag to GitHub
git push origin v0.9.0
```

### Step 8: Create GitHub Release

**Option A: Via GitHub Web Interface**

1. Go to https://github.com/YOUR-USERNAME/Maester-O365/releases/new
2. Choose tag: `v0.9.0`
3. Release title: `v0.9.0 - Maester Deployment Framework (Pre-Release)`
4. Description: Copy content from RELEASE-SUMMARY-v0.9.0.md
5. Check "This is a pre-release"
6. Click "Publish release"

**Option B: Via GitHub CLI**

```bash
# Create release from tag
gh release create v0.9.0 \
  --title "v0.9.0 - Maester Deployment Framework (Pre-Release)" \
  --notes-file RELEASE-SUMMARY-v0.9.0.md \
  --prerelease
```

### Step 9: Configure Repository Settings

```bash
# Set repository topics (via web interface or CLI)
gh repo edit --add-topic maester
gh repo edit --add-topic microsoft-365
gh repo edit --add-topic security-automation
gh repo edit --add-topic compliance
gh repo edit --add-topic vsphere
gh repo edit --add-topic kubernetes
gh repo edit --add-topic terraform
gh repo edit --add-topic remediation
gh repo edit --add-topic devops
gh repo edit --add-topic azure
gh repo edit --add-topic aws
gh repo edit --add-topic gcp

# Enable discussions
gh repo edit --enable-discussions

# Enable issues
gh repo edit --enable-issues

# Enable wiki
gh repo edit --enable-wiki
```

### Step 10: Set Up Branch Protection (Optional but Recommended)

Via GitHub Web Interface:
1. Go to Settings > Branches
2. Add branch protection rule for `main`
3. Configure:
   - [x] Require pull request reviews (1 approver)
   - [x] Require status checks to pass
   - [x] Require branches to be up to date
   - [x] Require conversation resolution before merging
   - [x] Include administrators

### Step 11: Configure GitHub Actions

```bash
# GitHub Actions should auto-detect .github/workflows/ci.yml
# Verify it's enabled:
# Settings > Actions > General > Allow all actions

# First push will trigger CI workflow
# Monitor at: https://github.com/YOUR-USERNAME/Maester-O365/actions
```

### Step 12: Create Initial Issues and Labels

**Create Standard Labels:**

```bash
# Via GitHub CLI
gh label create "bug" --color "d73a4a" --description "Something isn't working"
gh label create "enhancement" --color "a2eeef" --description "New feature or request"
gh label create "documentation" --color "0075ca" --description "Improvements or additions to documentation"
gh label create "good first issue" --color "7057ff" --description "Good for newcomers"
gh label create "help wanted" --color "008672" --description "Extra attention is needed"
gh label create "priority: critical" --color "b60205" --description "Critical priority"
gh label create "priority: high" --color "d93f0b" --description "High priority"
gh label create "priority: medium" --color "fbca04" --description "Medium priority"
gh label create "priority: low" --color "0e8a16" --description "Low priority"
gh label create "remediation" --color "c5def5" --description "Related to remediation engine"
gh label create "compliance" --color "bfdadc" --description "Related to compliance frameworks"
gh label create "vsphere" --color "1d76db" --description "vSphere-specific"
gh label create "serverless" --color "5319e7" --description "Serverless deployments"
```

---

## 🔄 Post-Release Activities

### Announce the Release

**1. Update Project README on GitHub**
- Ensure badges display correctly
- Verify all links work

**2. Social Media Announcements**

```markdown
🚀 Excited to announce v0.9.0 of the Maester Deployment Framework!

✨ Highlights:
- Multi-platform support (vSphere, Azure, AWS, GCP)
- Automated remediation engine
- 10 compliance frameworks mapped
- Serverless architectures
- 300+ pages of documentation

⭐ Star the repo: https://github.com/YOUR-USERNAME/Maester-O365

#Maester #Microsoft365 #Security #DevSecOps
```

**3. Community Channels**
- Discord: https://discord.gg/maester
- Reddit: r/sysadmin, r/kubernetes
- LinkedIn: Post announcement
- Twitter: @MaesterFramework

**4. Maester Community**
- Post in Maester Discord
- Update Maester.dev (if applicable)
- Notify Maester maintainers

### Set Up Monitoring

**GitHub Watch Settings:**
- Enable notifications for issues
- Enable notifications for pull requests
- Enable notifications for discussions

**Repository Insights:**
- Monitor traffic (Settings > Insights > Traffic)
- Track stars and forks
- Review clone statistics

---

## 📝 Future Commits Workflow

For future updates:

```bash
# Create feature branch
git checkout -b feature/new-feature

# Make changes
# ... edit files ...

# Commit changes
git add .
git commit -m "feat(scope): description"

# Push to GitHub
git push origin feature/new-feature

# Create pull request via GitHub or CLI
gh pr create --title "feat: New feature" --body "Description"

# After PR approval and merge, update local main
git checkout main
git pull origin main
```

---

## 🏷️ Version Tagging Strategy

### Semantic Versioning

We follow [Semantic Versioning](https://semver.org/):

- **v0.9.x** - Pre-release versions
- **v1.0.0** - First stable release
- **v1.x.0** - Minor version (new features)
- **v1.0.x** - Patch version (bug fixes)
- **v2.0.0** - Major version (breaking changes)

### Creating New Versions

```bash
# For patch release (v0.9.1)
git tag -a v0.9.1 -m "Release v0.9.1 - Bug fixes"
git push origin v0.9.1
gh release create v0.9.1 --notes "Bug fixes and improvements"

# For minor release (v0.10.0)
git tag -a v0.10.0 -m "Release v0.10.0 - New features"
git push origin v0.10.0
gh release create v0.10.0 --notes "New features"

# For major release (v1.0.0)
git tag -a v1.0.0 -m "Release v1.0.0 - Stable release"
git push origin v1.0.0
gh release create v1.0.0 --notes "First stable release"
```

---

## 🔧 Troubleshooting

### Problem: "Remote already exists"

```bash
# Remove existing remote
git remote remove origin

# Add correct remote
git remote add origin https://github.com/YOUR-USERNAME/Maester-O365.git
```

### Problem: "Permission denied (publickey)"

```bash
# Set up SSH key or use HTTPS with token
# For HTTPS:
git remote set-url origin https://YOUR-USERNAME@github.com/YOUR-USERNAME/Maester-O365.git

# Or use GitHub CLI
gh auth login
```

### Problem: "Rejected - non-fast-forward"

```bash
# Pull latest changes first
git pull origin main --rebase

# Then push
git push origin main
```

### Problem: ".gitignore not working"

```bash
# Remove cached files
git rm -r --cached .
git add .
git commit -m "chore: fix .gitignore"
```

---

## ✅ Verification Checklist

After release, verify:

- [ ] Repository is public/private as intended
- [ ] README displays correctly on GitHub
- [ ] All links work (no 404s)
- [ ] GitHub Actions CI runs successfully
- [ ] Release v0.9.0 is visible in Releases
- [ ] Tag v0.9.0 is created
- [ ] Topics/tags are set
- [ ] License is visible
- [ ] Issues are enabled
- [ ] Discussions are enabled (if desired)
- [ ] Branch protection is configured
- [ ] SECURITY.md is visible in Security tab

---

## 📚 Additional Resources

### Git Commands Reference

```bash
# View commit history
git log --oneline --graph --all

# View tags
git tag -l

# View remote information
git remote -v

# Check repository status
git status

# View differences
git diff

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1
```

### GitHub CLI Commands

```bash
# View repository
gh repo view

# View issues
gh issue list

# View pull requests
gh pr list

# View releases
gh release list

# View workflows
gh workflow list
```

---

## 🎉 You're Done!

Your Maester Deployment Framework v0.9.0 is now published to GitHub!

**Next Steps:**
1. Monitor GitHub for issues and PRs
2. Engage with community feedback
3. Plan v0.9.1 based on feedback
4. Continue development toward v1.0.0

---

**Questions?** Contact: adrian207@gmail.com

**Repository:** https://github.com/YOUR-USERNAME/Maester-O365

