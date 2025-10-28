# Docker Compose Deployment on Windows

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Version:** 0.9.1  
**Target**: Windows 10, Windows 11, Windows Server 2019+

---

## Overview

This guide provides Windows-specific instructions for deploying the Maester Deployment Framework using Docker Compose.

---

## Prerequisites

### Required Software

1. **Windows 10/11** (Pro, Enterprise, or Education) or **Windows Server 2019+**
   - 64-bit processor with Second Level Address Translation (SLAT)
   - 4GB system RAM minimum (8GB recommended)
   - BIOS-level hardware virtualization support enabled

2. **Docker Desktop for Windows** (Latest version)
   - Download: https://www.docker.com/products/docker-desktop/
   - WSL 2 backend (recommended) or Hyper-V backend

3. **PowerShell 7+** (Optional but recommended)
   - Download: https://github.com/PowerShell/PowerShell/releases

4. **Git for Windows**
   - Download: https://git-scm.com/download/win

---

## Installation Steps

### Step 1: Install Docker Desktop

1. Download Docker Desktop from https://www.docker.com/products/docker-desktop/
2. Run the installer
3. **Important**: During installation, ensure "Use WSL 2 instead of Hyper-V" is selected (recommended)
4. Restart your computer when prompted
5. Start Docker Desktop
6. Verify installation:

```powershell
docker --version
docker-compose --version
```

Expected output:
```
Docker version 24.0.x, build xxxxx
Docker Compose version v2.xx.x
```

### Step 2: Configure Docker Desktop for Windows

1. Open Docker Desktop
2. Go to Settings (gear icon)
3. **Resources** → **File Sharing**:
   - Add the drive where you'll clone the repository (e.g., `C:\`)
   - Click "Apply & Restart"

4. **Resources** → **Advanced**:
   - Memory: Allocate at least 4GB (8GB recommended)
   - CPUs: Allocate at least 2 cores (4 recommended)
   - Click "Apply & Restart"

5. **Docker Engine**:
   - Verify configuration looks similar to:
   ```json
   {
     "builder": {
       "gc": {
         "enabled": true
       }
     },
     "experimental": false
   }
   ```

---

## Deployment

### Clone Repository

```powershell
# Navigate to your preferred directory
cd C:\Github

# Clone the repository
git clone https://github.com/adrian207/Maester-O365.git
cd Maester-O365
```

### Configure Environment

1. Create `.env` file from example:

```powershell
# Copy example file
Copy-Item .env.example .env

# Edit with your favorite editor
notepad .env
# Or use VS Code
code .env
```

2. Update the following required values in `.env`:

```ini
# Azure AD Configuration
AZURE_TENANT_ID=your-tenant-id-here
AZURE_CLIENT_ID=your-client-id-here

# Database Password
DB_PASSWORD=YourSecurePassword123!

# Web Admin Password
WEB_ADMIN_PASSWORD=YourAdminPassword123!

# Notification Email
NOTIFICATION_EMAIL=security@yourcompany.com
```

### Start Services

```powershell
# Start all services in detached mode
docker-compose up -d

# View logs
docker-compose logs -f

# Check status
docker-compose ps
```

Expected output:
```
NAME                      STATUS              PORTS
maester-postgres          Up About a minute   0.0.0.0:5432->5432/tcp
maester-redis             Up About a minute   0.0.0.0:6379->6379/tcp
maester-runner            Up About a minute
maester-report-server     Up About a minute   0.0.0.0:8080->8080/tcp
maester-notification-hub  Up About a minute
maester-compliance-mapper Up About a minute
```

### Access Web Interface

Open your browser and navigate to:
```
http://localhost:8080
```

Default credentials:
- Username: `admin`
- Password: (from `WEB_ADMIN_PASSWORD` in `.env`)

---

## Windows-Specific Issues & Solutions

### Issue 1: Volume Mount Permissions

**Problem**: Error like "Permission denied: './data/postgres'"

**Solution**: The docker-compose.yml in v0.9.1+ uses Windows-compatible named volumes instead of bind mounts. If you're on v0.9.0, update docker-compose.yml:

```yaml
# OLD (doesn't work well on Windows)
volumes:
  - ./data/postgres:/var/lib/postgresql/data

# NEW (Windows-compatible)
volumes:
  - type: volume
    source: postgres_data
    target: /var/lib/postgresql/data
```

### Issue 2: Line Ending Errors

**Problem**: Error like "file not found" or scripts fail to execute

**Solution**: Configure Git to use Unix line endings:

```powershell
# Configure Git globally
git config --global core.autocrlf input

# Re-clone repository if already cloned
cd C:\Github
Remove-Item -Recurse -Force Maester-O365
git clone https://github.com/adrian207/Maester-O365.git
```

### Issue 3: Docker Desktop Not Starting

**Problem**: Docker Desktop fails to start with "WSL 2 installation incomplete"

**Solution**:

1. Open PowerShell as Administrator
2. Run:
```powershell
wsl --install
```
3. Restart computer
4. Start Docker Desktop

**Alternative**: If WSL 2 doesn't work, switch to Hyper-V:
1. Docker Desktop Settings → General
2. Uncheck "Use the WSL 2 based engine"
3. Click "Apply & Restart"

### Issue 4: Port Already in Use

**Problem**: Error like "port 8080 is already allocated"

**Solution**: Change the port in `.env`:

```ini
# Use a different port
WEB_PORT=8081
```

Then restart:
```powershell
docker-compose down
docker-compose up -d
```

### Issue 5: Slow Performance

**Problem**: Containers running slowly on Windows

**Solutions**:

1. **Increase Docker Resources**:
   - Docker Desktop → Settings → Resources
   - Memory: 8GB+
   - CPUs: 4+

2. **Use WSL 2 Backend** (faster than Hyper-V):
   - Docker Desktop → Settings → General
   - Check "Use the WSL 2 based engine"

3. **Store data in WSL 2** (if using WSL 2 backend):
```powershell
# Clone in WSL 2 instead of Windows filesystem
wsl
cd ~
git clone https://github.com/adrian207/Maester-O365.git
cd Maester-O365
docker-compose up -d
```

### Issue 6: Firewall Blocking Connections

**Problem**: Cannot access web interface at http://localhost:8080

**Solution**: Add firewall rule:

```powershell
# Run as Administrator
New-NetFirewallRule -DisplayName "Maester Web UI" -Direction Inbound -LocalPort 8080 -Protocol TCP -Action Allow
```

---

## PowerShell Script for Quick Setup

Save this as `deploy-maester.ps1`:

```powershell
# Maester Quick Setup Script for Windows
# Author: Adrian Johnson <adrian207@gmail.com>

#Requires -Version 7

Write-Host "Maester Deployment Framework - Windows Setup" -ForegroundColor Cyan
Write-Host "=============================================" -ForegroundColor Cyan

# Check Docker
Write-Host "`nChecking Docker..." -ForegroundColor Yellow
try {
    $dockerVersion = docker --version
    Write-Host "✓ Docker installed: $dockerVersion" -ForegroundColor Green
} catch {
    Write-Host "✗ Docker not found. Please install Docker Desktop first." -ForegroundColor Red
    exit 1
}

# Check Docker Compose
Write-Host "`nChecking Docker Compose..." -ForegroundColor Yellow
try {
    $composeVersion = docker-compose --version
    Write-Host "✓ Docker Compose installed: $composeVersion" -ForegroundColor Green
} catch {
    Write-Host "✗ Docker Compose not found." -ForegroundColor Red
    exit 1
}

# Check if Docker is running
Write-Host "`nChecking if Docker is running..." -ForegroundColor Yellow
try {
    docker ps | Out-Null
    Write-Host "✓ Docker is running" -ForegroundColor Green
} catch {
    Write-Host "✗ Docker is not running. Please start Docker Desktop." -ForegroundColor Red
    exit 1
}

# Create .env if it doesn't exist
if (-not (Test-Path ".env")) {
    Write-Host "`nCreating .env file..." -ForegroundColor Yellow
    if (Test-Path ".env.example") {
        Copy-Item ".env.example" ".env"
        Write-Host "✓ .env created from .env.example" -ForegroundColor Green
        Write-Host "! Please edit .env with your configuration" -ForegroundColor Yellow
        
        # Prompt for required values
        $tenantId = Read-Host "Enter your Azure Tenant ID"
        $clientId = Read-Host "Enter your Azure Client ID"
        
        # Update .env file
        (Get-Content ".env") -replace 'your-tenant-id-here', $tenantId | Set-Content ".env"
        (Get-Content ".env") -replace 'your-client-id-here', $clientId | Set-Content ".env"
        
        Write-Host "✓ .env updated with your values" -ForegroundColor Green
    } else {
        Write-Host "✗ .env.example not found" -ForegroundColor Red
        exit 1
    }
} else {
    Write-Host "`n✓ .env file already exists" -ForegroundColor Green
}

# Pull images
Write-Host "`nPulling Docker images..." -ForegroundColor Yellow
docker-compose pull

# Start services
Write-Host "`nStarting services..." -ForegroundColor Yellow
docker-compose up -d

# Wait for services to be ready
Write-Host "`nWaiting for services to be ready..." -ForegroundColor Yellow
Start-Sleep -Seconds 30

# Check status
Write-Host "`nService Status:" -ForegroundColor Yellow
docker-compose ps

Write-Host "`n=============================================" -ForegroundColor Cyan
Write-Host "Maester Deployment Framework is running!" -ForegroundColor Green
Write-Host "=============================================" -ForegroundColor Cyan
Write-Host "`nWeb Interface: http://localhost:8080" -ForegroundColor Cyan
Write-Host "Default Username: admin" -ForegroundColor Cyan
Write-Host "Default Password: (check your .env file)" -ForegroundColor Cyan
Write-Host "`nTo view logs: docker-compose logs -f" -ForegroundColor Yellow
Write-Host "To stop: docker-compose down" -ForegroundColor Yellow
Write-Host "`nPress any key to open web interface..." -ForegroundColor Yellow
$null = $Host.UI.RawUI.ReadKey('NoEcho,IncludeKeyDown')
Start-Process "http://localhost:8080"
```

Run with:
```powershell
.\deploy-maester.ps1
```

---

## Maintenance Commands

### View Logs
```powershell
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f maester-runner

# Last 100 lines
docker-compose logs --tail=100
```

### Restart Services
```powershell
# Restart all
docker-compose restart

# Restart specific service
docker-compose restart maester-runner
```

### Stop Services
```powershell
# Stop (keep data)
docker-compose stop

# Stop and remove containers (keep data)
docker-compose down

# Stop and remove everything including volumes (WARNING: deletes data)
docker-compose down -v
```

### Update to Latest Version
```powershell
# Pull latest code
git pull origin main

# Pull latest images
docker-compose pull

# Recreate containers
docker-compose up -d --force-recreate
```

### Backup Data
```powershell
# Create backup directory
New-Item -ItemType Directory -Path ".\backups" -Force

# Backup database
docker-compose exec -T postgres pg_dump -U maester maester > ".\backups\maester_$(Get-Date -Format 'yyyy-MM-dd').sql"

# Backup volumes
docker run --rm -v maester-o365_postgres_data:/data -v ${PWD}/backups:/backup alpine tar czf /backup/postgres_data.tar.gz /data
```

### Restore Data
```powershell
# Restore database
Get-Content ".\backups\maester_2025-10-28.sql" | docker-compose exec -T postgres psql -U maester maester
```

---

## Troubleshooting

### Check Container Health
```powershell
docker-compose ps
docker inspect --format='{{json .State.Health}}' maester-postgres
```

### Access Container Shell
```powershell
# PostgreSQL
docker-compose exec postgres psql -U maester

# Any container
docker-compose exec maester-runner pwsh
```

### Check Network Connectivity
```powershell
# Test from runner to postgres
docker-compose exec maester-runner Test-NetConnection postgres -Port 5432

# Test from runner to Graph API
docker-compose exec maester-runner curl https://graph.microsoft.com/v1.0
```

### Clean Docker System
```powershell
# Remove unused containers, networks, images
docker system prune -a

# Remove all stopped containers
docker container prune

# Remove unused volumes
docker volume prune
```

---

## Performance Tuning for Windows

### Optimize WSL 2 Settings

Create `.wslconfig` in `C:\Users\YourUsername\`:

```ini
[wsl2]
memory=8GB
processors=4
swap=2GB
localhostForwarding=true
```

Restart WSL:
```powershell
wsl --shutdown
```

### Disable Windows Defender Real-Time Scanning for Docker

1. Open Windows Security
2. Virus & threat protection → Manage settings
3. Add exclusions:
   - `C:\ProgramData\Docker`
   - `C:\Program Files\Docker`
   - Your project directory (e.g., `C:\Github\Maester-O365`)

---

## Next Steps

1. Review [Configuration Guide](../operations/configuration.md)
2. Set up [Notifications](../operations/notifications.md)
3. Create [Custom Tests](../development/custom-tests.md)
4. Configure [Compliance Mapping](../compliance/README.md)

---

## Support

**Issues?** Report at: https://github.com/adrian207/Maester-O365/issues

**Questions?** adrian207@gmail.com

---

**Last Updated**: 2025-10-28  
**Tested On**: Windows 10 22H2, Windows 11 23H2, Windows Server 2022

