# Serverless Architecture Guide

**Author:** Adrian Johnson <adrian207@gmail.com>  
**Version:** 0.9.0  
**Last Updated:** 2025-10-28

---

## Overview

This document provides comprehensive guidance on deploying the Maester Deployment Framework using serverless architectures across Azure, AWS, and Google Cloud Platform.

---

## Why Serverless?

### Benefits

**Cost Optimization**:
- Pay only for actual execution time
- No idle resource costs
- Automatic scaling included
- Ideal for periodic scheduled tasks

**Operational Simplicity**:
- Zero infrastructure management
- Automatic patching and updates
- Built-in high availability
- Native cloud service integration

**Rapid Deployment**:
- Deploy in minutes vs hours
- Simplified architecture
- No Kubernetes cluster management
- Faster iteration cycles

### Trade-offs

**Limitations**:
- Cold start latency (2-30 seconds)
- Execution time limits (typically 5-15 minutes)
- Memory constraints
- Vendor lock-in considerations

**Best Suited For**:
- Small to medium-sized organizations
- Cloud-native environments
- Budget-constrained deployments
- Testing and development

---

## Azure Serverless Architecture

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│              Azure Serverless Architecture                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Azure Timer Function                           │    │
│  │  Runtime: PowerShell 7.4                               │    │
│  │  Trigger: Timer (Cron: "0 2 * * *")                    │    │
│  │  Plan: Consumption or Premium                          │    │
│  │  Authentication: Managed Identity                      │    │
│  │                                                         │    │
│  │  Function Flow:                                        │    │
│  │    1. Trigger at scheduled time                        │    │
│  │    2. Acquire Managed Identity token                   │    │
│  │    3. Execute Maester tests                            │    │
│  │    4. Generate reports                                 │    │
│  │    5. Upload to Blob Storage                           │    │
│  │    6. Trigger downstream functions                     │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Azure Container Apps                           │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Report Server (Scale to Zero)                │    │    │
│  │  │  - React frontend + Node.js API               │    │    │
│  │  │  - Min instances: 0                           │    │    │
│  │  │  - Max instances: 10                          │    │    │
│  │  │  - Auto-scale on HTTP traffic                 │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Remediation Engine                           │    │    │
│  │  │  - PowerShell runtime                         │    │    │
│  │  │  - Event-driven (Queue trigger)               │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Notification Hub                             │    │    │
│  │  │  - Node.js runtime                            │    │    │
│  │  │  - Queue-based processing                     │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Azure Platform Services                        │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Cosmos DB (Serverless Mode)                  │    │    │
│  │  │  - Test result metadata                       │    │    │
│  │  │  - Compliance mappings                        │    │    │
│  │  │  - User sessions                              │    │    │
│  │  │  - Auto-scale RU/s                            │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Blob Storage                                 │    │    │
│  │  │  - Hot tier: Recent reports (90 days)        │    │    │
│  │  │  - Cool tier: Archive (91-365 days)          │    │    │
│  │  │  - Archive tier: Long-term (1-7 years)       │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Storage Queue                                │    │    │
│  │  │  - Remediation requests                       │    │    │
│  │  │  - Notification queue                         │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Key Vault                                    │    │    │
│  │  │  - Webhook URLs                               │    │    │
│  │  │  - SMTP credentials                           │    │    │
│  │  │  - API keys                                   │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Logic Apps                                   │    │    │
│  │  │  - Advanced notification workflows            │    │    │
│  │  │  - Teams/Slack integrations                   │    │    │
│  │  │  - Email with templates                       │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Application Insights                         │    │    │
│  │  │  - Function execution metrics                 │    │    │
│  │  │  - Performance monitoring                     │    │    │
│  │  │  - Alerting and diagnostics                   │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

### Component Details

#### Azure Functions (Maester Test Runner)

**Function Configuration**:

```json
{
  "name": "MaesterTestRunner",
  "type": "timerTrigger",
  "direction": "in",
  "schedule": "0 2 * * *",
  "runOnStartup": false,
  "useMonitor": true
}
```

**Function Code Structure**:

```powershell
# run.ps1
using namespace System.Net

param($Timer)

Write-Host "Maester Test Runner started at: $(Get-Date)"

try {
    # 1. Connect using Managed Identity
    Connect-MgGraph -Identity
    
    # 2. Import Maester module
    Import-Module Maester
    
    # 3. Load test configuration
    $testConfig = Get-Content "$env:HOME/config/test-config.json" | ConvertFrom-Json
    
    # 4. Execute tests
    $results = Invoke-MaesterTests `
        -TenantId $env:AZURE_TENANT_ID `
        -Tags $testConfig.TestTags `
        -OutputFormat "NUnitXml","JSON","HTML"
    
    # 5. Upload results to Blob Storage
    $storageAccount = $env:STORAGE_ACCOUNT_NAME
    $containerName = "maester-reports"
    $timestamp = Get-Date -Format "yyyy-MM-dd-HHmmss"
    
    # Upload HTML report
    $reportPath = "reports/$timestamp"
    Set-AzStorageBlobContent `
        -File $results.HtmlReport `
        -Container $containerName `
        -Blob "$reportPath/report.html" `
        -Context (New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount)
    
    # 6. Save metadata to Cosmos DB
    $cosmosEndpoint = $env:COSMOS_ENDPOINT
    $databaseName = "maester"
    $containerName = "test-results"
    
    $metadata = @{
        id = (New-Guid).ToString()
        timestamp = Get-Date -Format o
        totalTests = $results.TotalCount
        passed = $results.PassedCount
        failed = $results.FailedCount
        reportUrl = "https://$storageAccount.blob.core.windows.net/$containerName/$reportPath/report.html"
    }
    
    # Use Cosmos DB SDK or REST API
    Invoke-RestMethod `
        -Uri "$cosmosEndpoint/dbs/$databaseName/colls/$containerName/docs" `
        -Method POST `
        -Body ($metadata | ConvertTo-Json) `
        -Headers @{
            "Authorization" = Get-CosmosDBAuthToken
            "x-ms-documentdb-partitionkey" = "[`"$($metadata.id)`"]"
        }
    
    # 7. Trigger notification if failures
    if ($results.FailedCount -gt 0) {
        # Add message to Storage Queue for notification processing
        $queueName = "notification-queue"
        $message = @{
            type = "test_failure"
            failedCount = $results.FailedCount
            reportUrl = $metadata.reportUrl
        } | ConvertTo-Json
        
        # Queue message will trigger notification function
        Add-AzStorageQueueMessage `
            -Queue $queueName `
            -Message $message
    }
    
    Write-Host "Maester Test Runner completed successfully"
    
} catch {
    Write-Error "Error executing Maester tests: $_"
    throw
}
```

**Function Configuration (host.json)**:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  },
  "functionTimeout": "00:15:00",
  "managedDependency": {
    "enabled": true
  },
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond": 20
      }
    }
  }
}
```

**Resource Requirements**:
- **Memory**: 2048 MB (Premium Plan recommended)
- **Timeout**: 15 minutes
- **Plan**: Premium EP1 or Consumption

---

#### Azure Container Apps (Report Server)

**Container App Configuration**:

```yaml
apiVersion: apps/v1
kind: ContainerApp
metadata:
  name: maester-report-server
spec:
  configuration:
    activeRevisionsMode: Single
    ingress:
      external: true
      targetPort: 8080
      transport: http
      allowInsecure: false
  template:
    containers:
    - name: report-server
      image: maester/report-server:v0.9.0
      resources:
        cpu: 1.0
        memory: 2Gi
      env:
      - name: COSMOS_ENDPOINT
        value: https://maester-cosmos.documents.azure.com
      - name: STORAGE_ACCOUNT
        value: maesterreports
      - name: KEY_VAULT_URL
        value: https://maester-kv.vault.azure.net
    scale:
      minReplicas: 0
      maxReplicas: 10
      rules:
      - name: http-rule
        http:
          metadata:
            concurrentRequests: "50"
```

**Cost Optimization**:
- **Scale to Zero**: Automatically scales to 0 replicas when idle
- **Cold Start**: ~5-10 seconds
- **Active Hours**: Only pay when serving requests

---

### Deployment with Terraform

```hcl
# terraform/azure-serverless/main.tf

terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}

# Resource Group
resource "azurerm_resource_group" "maester" {
  name     = "rg-maester-serverless"
  location = "East US"
}

# Storage Account
resource "azurerm_storage_account" "maester" {
  name                     = "maesterreports${random_string.suffix.result}"
  resource_group_name      = azurerm_resource_group.maester.name
  location                 = azurerm_resource_group.maester.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
  
  blob_properties {
    versioning_enabled = true
    
    delete_retention_policy {
      days = 30
    }
  }
}

# Cosmos DB (Serverless)
resource "azurerm_cosmosdb_account" "maester" {
  name                = "maester-cosmos-${random_string.suffix.result}"
  location            = azurerm_resource_group.maester.location
  resource_group_name = azurerm_resource_group.maester.name
  offer_type          = "Standard"
  
  capabilities {
    name = "EnableServerless"
  }
  
  consistency_policy {
    consistency_level = "Session"
  }
  
  geo_location {
    location          = azurerm_resource_group.maester.location
    failover_priority = 0
  }
}

# Function App (Consumption Plan)
resource "azurerm_service_plan" "maester" {
  name                = "asp-maester-consumption"
  resource_group_name = azurerm_resource_group.maester.name
  location            = azurerm_resource_group.maester.location
  os_type             = "Linux"
  sku_name            = "Y1"  # Consumption
}

resource "azurerm_linux_function_app" "maester" {
  name                = "func-maester-${random_string.suffix.result}"
  resource_group_name = azurerm_resource_group.maester.name
  location            = azurerm_resource_group.maester.location
  
  service_plan_id            = azurerm_service_plan.maester.id
  storage_account_name       = azurerm_storage_account.maester.name
  storage_account_access_key = azurerm_storage_account.maester.primary_access_key
  
  identity {
    type = "SystemAssigned"
  }
  
  site_config {
    application_stack {
      powershell_core_version = "7.4"
    }
    
    application_insights_key = azurerm_application_insights.maester.instrumentation_key
  }
  
  app_settings = {
    "AZURE_TENANT_ID"           = var.tenant_id
    "STORAGE_ACCOUNT_NAME"      = azurerm_storage_account.maester.name
    "COSMOS_ENDPOINT"           = azurerm_cosmosdb_account.maester.endpoint
    "KEY_VAULT_URL"             = azurerm_key_vault.maester.vault_uri
    "FUNCTIONS_WORKER_RUNTIME"  = "powershell"
  }
}

# Application Insights
resource "azurerm_application_insights" "maester" {
  name                = "appi-maester"
  location            = azurerm_resource_group.maester.location
  resource_group_name = azurerm_resource_group.maester.name
  application_type    = "web"
}

# Key Vault
resource "azurerm_key_vault" "maester" {
  name                       = "kv-maester-${random_string.suffix.result}"
  location                   = azurerm_resource_group.maester.location
  resource_group_name        = azurerm_resource_group.maester.name
  tenant_id                  = data.azurerm_client_config.current.tenant_id
  sku_name                   = "standard"
  soft_delete_retention_days = 7
  purge_protection_enabled   = false
}

# Grant Function App access to Key Vault
resource "azurerm_key_vault_access_policy" "function" {
  key_vault_id = azurerm_key_vault.maester.id
  tenant_id    = data.azurerm_client_config.current.tenant_id
  object_id    = azurerm_linux_function_app.maester.identity[0].principal_id
  
  secret_permissions = [
    "Get",
    "List"
  ]
}

# Container Apps Environment
resource "azurerm_container_app_environment" "maester" {
  name                       = "cae-maester"
  location                   = azurerm_resource_group.maester.location
  resource_group_name        = azurerm_resource_group.maester.name
  log_analytics_workspace_id = azurerm_log_analytics_workspace.maester.id
}

# Log Analytics Workspace
resource "azurerm_log_analytics_workspace" "maester" {
  name                = "law-maester"
  location            = azurerm_resource_group.maester.location
  resource_group_name = azurerm_resource_group.maester.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}

# Container App (Report Server)
resource "azurerm_container_app" "report_server" {
  name                         = "ca-maester-reports"
  container_app_environment_id = azurerm_container_app_environment.maester.id
  resource_group_name          = azurerm_resource_group.maester.name
  revision_mode                = "Single"
  
  template {
    container {
      name   = "report-server"
      image  = "maester/report-server:v0.9.0"
      cpu    = 1.0
      memory = "2Gi"
      
      env {
        name  = "COSMOS_ENDPOINT"
        value = azurerm_cosmosdb_account.maester.endpoint
      }
      
      env {
        name  = "STORAGE_ACCOUNT"
        value = azurerm_storage_account.maester.name
      }
    }
    
    min_replicas = 0
    max_replicas = 10
  }
  
  ingress {
    external_enabled = true
    target_port      = 8080
    
    traffic_weight {
      percentage      = 100
      latest_revision = true
    }
  }
  
  identity {
    type = "SystemAssigned"
  }
}

# Random suffix for unique names
resource "random_string" "suffix" {
  length  = 6
  special = false
  upper   = false
}

data "azurerm_client_config" "current" {}

# Outputs
output "function_app_url" {
  value = azurerm_linux_function_app.maester.default_hostname
}

output "report_server_url" {
  value = azurerm_container_app.report_server.ingress[0].fqdn
}

output "storage_account_name" {
  value = azurerm_storage_account.maester.name
}
```

---

### Cost Estimate (Azure Serverless)

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **Azure Functions** | Consumption Plan, 30 daily executions, 5 min each | ~$5-10 |
| **Container Apps** | 2 instances, scale to zero, 50 hours active/month | ~$15-25 |
| **Cosmos DB** | Serverless mode, 10GB storage, 100K RU/s | ~$5-15 |
| **Blob Storage** | 100GB Hot tier, 1TB Cool tier | ~$20-35 |
| **Application Insights** | 5GB data ingestion/month | ~$10-15 |
| **Key Vault** | Standard tier | ~$1 |
| **Logic Apps** | 100 actions/month | ~$1-5 |
| **Total** | | **$57-106/month** |

---

## AWS Serverless Architecture

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│               AWS Serverless Architecture                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Lambda Function (Container)                    │    │
│  │  Runtime: PowerShell 7.4 via Container Image          │    │
│  │  Trigger: EventBridge (Cron: "0 2 * * ? *")           │    │
│  │  Memory: 3008 MB                                       │    │
│  │  Timeout: 15 minutes                                   │    │
│  │  Authentication: IAM Role (IRSA)                       │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Step Functions (Orchestration)                 │    │
│  │  - Handle long-running tests (>15 min)                │    │
│  │  - Chain multiple Lambda invocations                  │    │
│  │  - Error handling and retries                         │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Fargate (Report Server)                        │    │
│  │  - Application Load Balancer                          │    │
│  │  - Auto-scaling (0-10 tasks)                          │    │
│  │  - HTTPS with ACM certificate                         │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         AWS Services                                   │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  DynamoDB (On-Demand)                         │    │    │
│  │  │  - Test result metadata                       │    │    │
│  │  │  - Point-in-time recovery                     │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  S3 Buckets                                   │    │    │
│  │  │  - Standard: Recent reports (90 days)        │    │    │
│  │  │  - Intelligent-Tiering: Auto-optimization    │    │    │
│  │  │  - Glacier: Archive (1-7 years)              │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  SQS Queues                                   │    │    │
│  │  │  - Remediation queue                          │    │    │
│  │  │  - Notification queue                         │    │    │
│  │  │  - Dead letter queues                         │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Secrets Manager                              │    │    │
│  │  │  - Webhook URLs                               │    │    │
│  │  │  - SMTP credentials                           │    │    │
│  │  │  - API keys                                   │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  SNS / SES                                    │    │    │
│  │  │  - Email notifications                        │    │    │
│  │  │  - SMS alerts                                 │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  CloudWatch                                   │    │    │
│  │  │  - Logs                                       │    │    │
│  │  │  - Metrics                                    │    │    │
│  │  │  - Alarms                                     │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

### Lambda Function (Container-based)

**Dockerfile**:

```dockerfile
FROM public.ecr.aws/lambda/powershell:7.4

# Install required modules
RUN pwsh -Command " \
    Install-Module -Name Maester -Force -Scope CurrentUser; \
    Install-Module -Name Microsoft.Graph -Force -Scope CurrentUser; \
    Install-Module -Name AWS.Tools.Common -Force -Scope CurrentUser; \
    Install-Module -Name AWS.Tools.S3 -Force -Scope CurrentUser; \
    Install-Module -Name AWS.Tools.DynamoDBv2 -Force -Scope CurrentUser"

# Copy function code
COPY function/ ${LAMBDA_TASK_ROOT}/

# Set handler
CMD ["MaesterTestRunner::MaesterTestRunner.Handler::Invoke"]
```

**Handler Code**:

```powershell
# MaesterTestRunner.ps1
function Invoke-Handler {
    param(
        $LambdaInput,
        $LambdaContext
    )
    
    Write-Host "Maester Test Runner (Lambda) started"
    
    try {
        # 1. Authenticate using IAM Role (IRSA)
        Connect-MgGraph -Identity
        
        # 2. Execute Maester tests
        Import-Module Maester
        $results = Invoke-MaesterTests -OutputFormat "JSON","HTML"
        
        # 3. Upload to S3
        $bucketName = $env:S3_BUCKET_NAME
        $timestamp = Get-Date -Format "yyyy-MM-dd-HHmmss"
        $key = "reports/$timestamp/report.html"
        
        Write-S3Object `
            -BucketName $bucketName `
            -Key $key `
            -File $results.HtmlReport
        
        # 4. Save metadata to DynamoDB
        $tableName = $env:DYNAMODB_TABLE_NAME
        $item = @{
            Id = @{ S = (New-Guid).ToString() }
            Timestamp = @{ S = (Get-Date -Format o) }
            TotalTests = @{ N = $results.TotalCount.ToString() }
            Passed = @{ N = $results.PassedCount.ToString() }
            Failed = @{ N = $results.FailedCount.ToString() }
            ReportUrl = @{ S = "https://$bucketName.s3.amazonaws.com/$key" }
        }
        
        Write-DDBItem -TableName $tableName -Item $item
        
        # 5. Send notification if failures
        if ($results.FailedCount -gt 0) {
            $queueUrl = $env:SQS_QUEUE_URL
            $message = @{
                Type = "TestFailure"
                FailedCount = $results.FailedCount
                ReportUrl = $item.ReportUrl.S
            } | ConvertTo-Json
            
            Send-SQSMessage -QueueUrl $queueUrl -MessageBody $message
        }
        
        return @{
            statusCode = 200
            body = "Test execution completed successfully"
        }
        
    } catch {
        Write-Error "Error: $_"
        throw
    }
}
```

---

### Cost Estimate (AWS Serverless)

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **Lambda** | 30 executions/month, 3GB memory, 5 min duration | ~$10-15 |
| **Fargate** | 2 tasks, 1 vCPU, 2GB RAM, 50 hours/month | ~$25-35 |
| **DynamoDB** | On-Demand mode, 10GB storage, 100K RU | ~$5-12 |
| **S3** | 100GB Standard, 1TB Intelligent-Tiering | ~$23-40 |
| **CloudWatch** | 5GB logs, 100 metrics | ~$10-15 |
| **SQS** | 100K requests/month | ~$0.50 |
| **Secrets Manager** | 5 secrets | ~$2 |
| **Total** | | **$75-120/month** |

---

## Google Cloud Serverless Architecture

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│              GCP Serverless Architecture                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Cloud Functions (2nd Gen)                      │    │
│  │  Runtime: PowerShell via Container                     │    │
│  │  Trigger: Cloud Scheduler                              │    │
│  │  Memory: 4 GiB                                         │    │
│  │  Timeout: 60 minutes (2nd gen)                         │    │
│  │  Authentication: Workload Identity                     │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         Cloud Run (Report Server)                      │    │
│  │  - Fully managed containers                            │    │
│  │  - Scale to zero                                       │    │
│  │  - HTTPS endpoints                                     │    │
│  │  - Cloud Load Balancing                                │    │
│  └────────────────┬───────────────────────────────────────┘    │
│                   │                                              │
│                   ↓                                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         GCP Services                                   │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Firestore (Native mode)                      │    │    │
│  │  │  - Automatic scaling                          │    │    │
│  │  │  - ACID transactions                          │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Cloud Storage                                │    │    │
│  │  │  - Standard: Recent reports                   │    │    │
│  │  │  - Nearline: 30-90 days                       │    │    │
│  │  │  - Coldline: 90-365 days                      │    │    │
│  │  │  - Archive: 1-7 years                         │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Pub/Sub                                      │    │    │
│  │  │  - Notification topics                        │    │    │
│  │  │  - Dead letter topics                         │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Secret Manager                               │    │    │
│  │  │  - Webhook secrets                            │    │    │
│  │  │  - API keys                                   │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  │                                                         │    │
│  │  ┌───────────────────────────────────────────────┐    │    │
│  │  │  Cloud Logging & Monitoring                   │    │    │
│  │  │  - Centralized logs                           │    │    │
│  │  │  - Metrics and dashboards                     │    │    │
│  │  │  - Alerting policies                          │    │    │
│  │  └───────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

### Cost Estimate (GCP Serverless)

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| **Cloud Functions** | 30 executions, 4GB memory, 5 min duration | ~$8-12 |
| **Cloud Run** | 2 instances, 1 vCPU, 2GB RAM, 50 hours/month | ~$20-30 |
| **Firestore** | 10GB storage, 100K reads, 10K writes | ~$3-8 |
| **Cloud Storage** | 100GB Standard, 1TB Nearline | ~$20-35 |
| **Cloud Logging** | 5GB logs/month | ~$2.50 |
| **Pub/Sub** | 100K messages/month | ~$0.50 |
| **Secret Manager** | 5 secrets | ~$0.30 |
| **Total** | | **$54-89/month** |

---

## Comparison Matrix

| Feature | Azure | AWS | GCP |
|---------|-------|-----|-----|
| **Monthly Cost** | $57-106 | $75-120 | $54-89 |
| **Setup Complexity** | Medium | Medium | Low |
| **Cold Start** | 2-5 sec | 3-8 sec | 2-4 sec |
| **Max Timeout** | 10 min (Premium: 60 min) | 15 min | 60 min (2nd gen) |
| **Max Memory** | 4 GB | 10 GB | 32 GB |
| **Native M365 Integration** | Excellent | Good | Good |
| **Container Support** | Yes (Container Apps) | Yes (Fargate) | Yes (Cloud Run) |
| **Managed Identity** | Yes | Yes (IRSA) | Yes (Workload Identity) |
| **Best For** | Azure-first orgs | AWS-native shops | Cost-sensitive, GCP users |

---

## Serverless Best Practices

### 1. Cold Start Optimization

**Minimize Package Size**:
```powershell
# Only import required modules
Import-Module Maester -Function Invoke-MaesterTests
Import-Module Microsoft.Graph.Authentication -Function Connect-MgGraph
```

**Use Provisioned Concurrency** (AWS Lambda):
```hcl
resource "aws_lambda_provisioned_concurrency_config" "maester" {
  function_name                     = aws_lambda_function.maester.function_name
  provisioned_concurrent_executions = 1
  qualifier                         = aws_lambda_alias.live.name
}
```

**Azure Functions: Premium Plan**:
- Pre-warmed instances
- VNet integration
- Longer timeout (60 min)

---

### 2. Error Handling & Retries

**Implement Retry Logic**:
```powershell
function Invoke-WithRetry {
    param(
        [scriptblock]$ScriptBlock,
        [int]$MaxRetries = 3
    )
    
    $attempt = 0
    while ($attempt -lt $MaxRetries) {
        try {
            return & $ScriptBlock
        } catch {
            $attempt++
            if ($attempt -eq $MaxRetries) { throw }
            Start-Sleep -Seconds ([math]::Pow(2, $attempt))
        }
    }
}
```

---

### 3. Monitoring & Alerting

**CloudWatch Alarms (AWS)**:
```hcl
resource "aws_cloudwatch_metric_alarm" "lambda_errors" {
  alarm_name          = "maester-lambda-errors"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "1"
  metric_name         = "Errors"
  namespace           = "AWS/Lambda"
  period              = "300"
  statistic           = "Sum"
  threshold           = "0"
  alarm_description   = "Alert on Lambda errors"
  alarm_actions       = [aws_sns_topic.alerts.arn]
}
```

---

## Next Steps

- Review [Azure Serverless Deployment Guide](../deployment-guides/azure-serverless.md)
- Review [AWS Serverless Deployment Guide](../deployment-guides/aws-serverless.md)
- Review [GCP Serverless Deployment Guide](../deployment-guides/gcp-serverless.md)
- See [Cost Optimization Guide](../operations/cost-optimization.md)

---

**Document Status**: Pre-Release (v0.9.0)  
**Target Audience**: Cloud Architects, DevOps Engineers  
**Prerequisites**: Basic understanding of serverless architectures

