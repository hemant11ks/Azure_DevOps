# Microsoft Azure Services — DevOps & Cloud Engineering Guide

> **Purpose:** A category-wise, interview-ready reference to Microsoft Azure cloud services and the five core Azure DevOps services, with real-world use cases, common AWS equivalents, commands, and a CI/CD example.
>
> **Audience:** DevOps Engineers, SREs, Platform Engineers, Cloud Engineers  
> **Updated:** November 2026  
> **Official references:** [Azure products](https://azure.microsoft.com/en-us/products/) · [Azure documentation](https://learn.microsoft.com/en-us/azure/) · [Azure DevOps documentation](https://learn.microsoft.com/en-us/azure/devops/)

---

## Table of Contents

1. [Azure vs. Azure DevOps](#1-azure-vs-azure-devops)
2. [Azure Resource Hierarchy](#2-azure-resource-hierarchy)
3. [Five Core Azure DevOps Services](#3-five-core-azure-devops-services)
4. [Azure Cloud Services by Category](#4-azure-cloud-services-by-category)
5. [AWS vs. Azure Mapping](#5-aws-vs-azure-mapping)
6. [End-to-End DevOps Architecture](#6-end-to-end-devops-architecture)
7. [Basic Azure CLI Commands](#7-basic-azure-cli-commands)
8. [Sample Azure Pipelines YAML](#8-sample-azure-pipelines-yaml)
9. [Hands-On Labs and Learning Roadmap](#9-hands-on-labs-and-learning-roadmap)
10. [Azure DevOps Interview Questions](#10-azure-devops-interview-questions)
11. [Security, Reliability and Cost Checklist](#11-security-reliability-and-cost-checklist)
12. [Official Documentation](#12-official-documentation)

---

## 1. Azure vs. Azure DevOps

| Platform | What it provides | Typical use |
|---|---|---|
| **Microsoft Azure** | Cloud computing, networking, Kubernetes, databases, storage, identity, security, observability | Run and manage application infrastructure |
| **Azure DevOps Services** | Boards, Repos, Pipelines, Artifacts, Test Plans | Plan, build, test, package and deploy software |
| **Azure DevOps Server** | Self-hosted edition of Azure DevOps | Run DevOps collaboration tools in a customer-managed environment |
| **GitHub / GitHub Actions** | Git hosting and workflow automation | Alternative or complementary tools; can deploy to Azure |

**Important:** Azure DevOps Pipelines can deploy to **Azure, AWS, GCP, Kubernetes, on-premises servers**, and other targets. Azure cloud resources can also be managed by Jenkins, GitHub Actions, GitLab CI/CD, or Terraform. Azure and Azure DevOps are related but not the same product.

## 2. Azure Resource Hierarchy

```text
Microsoft Entra tenant (identity boundary)
└── Management Groups (optional grouping for governance)
    └── Azure Subscription (billing and resource boundary)
        └── Resource Group (logical lifecycle grouping)
            ├── Virtual Network (VNet)
            ├── Virtual Machine (VM)
            ├── Azure Kubernetes Service (AKS)
            └── Storage Account
```

- **Tenant:** Organization's Microsoft Entra ID identity directory.
- **Management group:** Group multiple subscriptions to apply governance at scale.
- **Subscription:** Controls access, quotas and billing for deployed resources.
- **Resource group:** Logical container for Azure resources; a resource belongs to one resource group at a time.
- **Region:** Geographic area where resources are deployed (for example, Central India).
- **Availability zone:** Physically separate datacenter grouping within a supported region for higher resilience.
- **Azure RBAC:** Defines who can perform actions at management-group, subscription, resource-group or resource scopes.

> **Interview tip:** An Azure DevOps **project** is not the same as an Azure **resource group**. The former manages a software team's work and delivery; the latter groups Azure cloud resources.

## 3. Five Core Azure DevOps Services

| Service | Main purpose | Real-world example | Rough comparison |
|---|---|---|---|
| **Azure Boards** | Work items, backlogs, sprints, bugs, Kanban | Track build failure investigation and release tasks | Jira |
| **Azure Repos** | Git repositories, branches, pull requests, branch policies | Review a feature branch before merging into `main` | GitHub, GitLab, Gerrit |
| **Azure Pipelines** | Continuous integration and delivery/deployment | Run tests, build Docker image, scan and deploy to AKS | Jenkins, GitLab CI, GitHub Actions |
| **Azure Artifacts** | Package feeds and package version management | Publish internal NuGet, npm, Maven, Python or Universal Packages | Nexus Repository, Artifactory |
| **Azure Test Plans** | Test planning, manual/exploratory testing | Track release acceptance test cases | Manual test management tooling |

### Azure DevOps concepts beyond the five services

| Concept | Meaning / use case |
|---|---|
| **Organization** | Top-level Azure DevOps tenant-like container for projects, users and settings |
| **Project** | Team workspace containing Repos, Boards, Pipelines and other services |
| **Pipeline** | Automated stages, jobs and steps, commonly described in `azure-pipelines.yml` |
| **Microsoft-hosted agent** | Temporary Microsoft-managed machine executing a pipeline job |
| **Self-hosted agent** | Your own VM/server executing jobs; useful for custom tools and private networks |
| **Agent pool** | Collection of agents available to pipelines |
| **Service connection** | Authenticated connection to external services, including Azure Resource Manager, ACR and Kubernetes |
| **Variable group** | Shared pipeline variables; sensitive values should be secret-protected |
| **Environment** | Deployment target grouping; supports approvals, checks and deployment history |
| **Pipeline artifact** | Files output by a pipeline run, e.g. compiled binary or build logs; distinct from Azure Artifacts package feeds |
| **Branch policy** | Controls such as required PR reviewers, build validation and protected branches |
| **Approvals and checks** | Gate deployments to sensitive environments |

### Typical Azure DevOps workflow

```text
Boards (task)
  ↓
Repos (branch + pull request)
  ↓
Pipelines CI (build + unit tests + security checks)
  ↓
Build output → Azure Artifacts / pipeline artifacts / ACR
  ↓
Pipelines CD (deploy to staging → validation → production approval)
  ↓
Azure App Service / VMs / AKS
  ↓
Azure Monitor + Application Insights (operate and troubleshoot)
```

---

## 4. Azure Cloud Services by Category

The **AWS equivalents are approximate**, not guaranteed one-to-one feature matches.

### A. Compute

| Azure service | What it does | Example DevOps use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure Virtual Machines (VMs)** | Runs Linux/Windows virtual servers | Self-hosted build agent or application server | EC2 |
| **Virtual Machine Scale Sets (VMSS)** | Manages and scales groups of VMs | Autoscale a fleet of worker VMs | EC2 Auto Scaling |
| **Azure App Service** | Managed web/API application hosting | Deploy a Python/Java/.NET web service without operating VMs | Elastic Beanstalk / App Runner |
| **Azure Functions** | Event-driven serverless code | Trigger a function on a queue message or webhook | Lambda |
| **Azure Batch** | Parallel/batch job scheduling | Parallel test execution or data jobs | AWS Batch |

### B. Containers and Kubernetes

| Azure service | What it does | Example DevOps use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure Kubernetes Service (AKS)** | Managed Kubernetes control plane and cluster integrations | Deploy microservices with Helm | EKS |
| **Azure Container Registry (ACR)** | Private OCI/Docker image registry | Push container images from Azure Pipelines | ECR |
| **Azure Container Instances (ACI)** | Run individual containers without managing VMs | Short-lived utility container | Fargate-style container execution |
| **Azure Container Apps** | Managed serverless container app platform | Autoscaled API or background processor | App Runner / ECS with Fargate |
| **Azure Kubernetes Fleet Manager** | Coordinate policy and upgrades across Kubernetes clusters | Multi-cluster operations | No exact single equivalent |

**Remember:** Docker creates/pushes images; **ACR stores images**; **AKS schedules and runs containers**; **Helm packages Kubernetes resources**.

### C. Networking

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Virtual Network (VNet)** | Private IP network | Isolate app, database and cluster subnets | VPC |
| **Network Security Group (NSG)** | Stateful allow/deny rules for network interfaces/subnets | Restrict incoming SSH and application traffic | Security Groups (partial analogy) |
| **Azure Load Balancer** | Layer 4 load balancing | Distribute TCP traffic to backend VMs | Network Load Balancer |
| **Application Gateway** | Layer 7 HTTP(S) load balancing with optional WAF | Route `/api` to an API backend | Application Load Balancer + WAF |
| **Azure Front Door** | Global HTTP(S) delivery, acceleration and security | Route users to nearest healthy app endpoint | CloudFront + global routing features |
| **Azure DNS** | DNS hosting | Point domain to application endpoint | Route 53 |
| **VPN Gateway** | Encrypted hybrid connectivity | Connect office network to Azure VNet | Site-to-Site VPN |
| **ExpressRoute** | Private dedicated connectivity to Azure | Enterprise datacenter ↔ Azure | Direct Connect |
| **Azure Bastion** | Managed browser-based SSH/RDP entry | Reach VM without public SSH/RDP exposure | EC2 Instance Connect Endpoint / SSM Session Manager (partial analogy) |
| **Azure Private Link / Private Endpoint** | Reach supported services over private IP connectivity | Privately access Key Vault, ACR, Storage | PrivateLink / VPC endpoints |
| **Azure Firewall** | Centrally managed network filtering | Inspect and restrict egress | Network Firewall |
| **Azure NAT Gateway** | Scalable outbound SNAT | Private subnet outbound internet access | NAT Gateway |
| **Network Watcher** | Network monitoring and diagnostics | Diagnose next-hop or NSG connectivity issues | VPC Reachability Analyzer + Flow Logs |

### D. Storage, Backup and DR

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure Blob Storage** | Object storage | Store logs, build outputs, backups | S3 |
| **Azure Files** | Managed SMB/NFS file shares | Shared build files and mounts | EFS / FSx (depends on protocol) |
| **Azure Managed Disks** | Persistent VM block disks | OS and data volumes | EBS |
| **Azure Queue Storage** | Basic asynchronous queue | Queue lightweight background tasks | SQS |
| **Azure Data Lake Storage** | Data-lake storage features on Blob Storage | Analytics and log datasets | S3-based data lake |
| **Azure Backup** | Backups and restore management | VM recovery and retention | AWS Backup |
| **Azure Site Recovery** | Replication and disaster recovery orchestration | Fail over selected workloads | Elastic Disaster Recovery |

### E. Databases and Caching

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure SQL Database** | Managed SQL Server database | Store transactional app data | RDS for SQL Server / Aurora-like managed model |
| **Azure Database for PostgreSQL** | Managed PostgreSQL | Backend for application microservices | RDS for PostgreSQL |
| **Azure Database for MySQL** | Managed MySQL | Web application database | RDS for MySQL |
| **Azure Cosmos DB** | Globally distributed NoSQL database offerings | High-scale global application data | DynamoDB (partial analogy) |
| **Azure Managed Redis** | Managed Redis-compatible caching | Session caching, low-latency access | ElastiCache for Redis/Valkey |

> **Service lifecycle note (November 2026):** Azure Cache for Redis is on a retirement path. For new learning and architecture design, prioritize **Azure Managed Redis**; check Microsoft's migration timeline for existing Azure Cache for Redis deployments.

### F. Identity, Secrets and Security

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Microsoft Entra ID** | Identity directory and authentication | User, workload and application authentication | IAM Identity Center / external identity integrations (not identical) |
| **Azure RBAC** | Authorizes actions on Azure resources | Give CI identity limited access to one resource group | IAM roles and policies |
| **Managed Identities** | Azure-managed workload identity for supported resources | VM/AKS app accesses Key Vault without long-lived client secrets | IAM roles for workloads (partial analogy) |
| **Azure Key Vault** | Secrets, keys and certificates | Retrieve DB password and signing certificate | Secrets Manager + KMS |
| **Microsoft Defender for Cloud** | Security posture, recommendations, workload protection | Detect cloud misconfigurations and risks | Security Hub + GuardDuty / CSPM features |
| **Microsoft Sentinel** | SIEM/SOAR security operations | Centralized security alert correlation | Security Lake + other security services |
| **Azure DDoS Protection** | DDoS defense | Protect public endpoints | Shield |
| **Azure Web Application Firewall (WAF)** | Web attack filtering | Protect HTTP apps | AWS WAF |

**Best practice:** Prefer workload identity federation or managed identity over long-lived service-principal secrets wherever supported. Use least-privilege RBAC.

### G. Monitoring, Logging and Observability

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure Monitor** | Unified metrics, logs, alerts | Track VM health and AKS performance | CloudWatch |
| **Log Analytics Workspace** | Central Log Analytics data store and KQL querying | Query cluster/application logs | CloudWatch Logs / Logs Insights |
| **Application Insights** | Application telemetry and distributed diagnostics | Trace failing API calls and dependency latency | X-Ray + CloudWatch Application Signals |
| **Azure Monitor Alerts** | Metric/log alerts | Page team when error rate rises | CloudWatch Alarms |
| **Azure Managed Grafana** | Managed Grafana visualization | Dashboards for SRE team | Amazon Managed Grafana |
| **Azure Monitor managed service for Prometheus** | Managed Prometheus metric collection/query | Kubernetes metrics and PromQL | Amazon Managed Service for Prometheus |
| **Azure Service Health** | Azure service incident and maintenance information | Investigate Azure-region incidents | AWS Health |

**SRE concepts to practise:** SLI/SLO, p95 latency, error rate, request throughput, saturation, alert thresholds, incident response, MTTR and postmortems.

### H. Infrastructure as Code, Automation and Governance

| Azure service/tool | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure Resource Manager (ARM)** | Azure control plane and deployment model | Define/manage resources via API/templates | CloudFormation control/deployment model |
| **Bicep** | Azure-native declarative IaC language | Provision VNets, VMs, ACR and AKS | CloudFormation templates |
| **Terraform + AzureRM provider** | Third-party, multi-cloud declarative IaC | Provision resource groups, networks and AKS | Terraform on AWS (same tool) |
| **Azure Policy** | Evaluate/enforce resource compliance | Restrict resource regions or require tags | Config / Organizations policies (partial) |
| **Azure Cost Management + Billing** | Track cloud usage and cost | Detect spend growth from always-on AKS | Cost Explorer / Budgets |
| **Azure Automation** | Runbooks and process automation | Scheduled maintenance and scripts | Systems Manager Automation |
| **Azure Resource Graph** | Query inventory across subscriptions | Find untagged VMs and unused resources | AWS Config advanced queries (partial) |

**Note:** Terraform, Docker, Kubernetes and Helm are open-source/third-party ecosystem tools, **not Azure services**.

### I. Integration, API and Messaging

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Azure Service Bus** | Reliable enterprise queues/topics | Order-processing messages | SQS + SNS (approx.) |
| **Azure Event Grid** | Event routing and subscriptions | Trigger processing when blob arrives | EventBridge |
| **Azure Event Hubs** | High-throughput event streaming | Ingest telemetry streams | Kinesis Data Streams |
| **Azure Logic Apps** | Low-code integrations/workflows | Automation across enterprise systems | Step Functions + integrations (partial) |
| **Azure API Management** | API gateway, policies, developer portal | Publish and secure internal/external APIs | API Gateway |

### J. AI and Machine Learning

| Azure service | What it does | Real-world use case | Approx. AWS equivalent |
|---|---|---|---|
| **Microsoft Foundry** | Build, manage and operate AI apps and agents | Internal engineering assistant | Bedrock ecosystem (approx.) |
| **Azure OpenAI** | Use available OpenAI models with Azure | Log-summarization or incident-triage assistant | Bedrock model access (approx.) |
| **Azure Machine Learning** | ML experimentation, training and deployment | Serve a model for anomaly detection | SageMaker |
| **Azure AI Search** | Indexing, retrieval, hybrid/vector search | Retrieval-augmented generation (RAG) | OpenSearch / Kendra (partial) |

---

## 5. AWS vs. Azure Mapping

Quick memory sheet for technical interviews:

| Requirement | Azure | AWS |
|---|---|---|
| Virtual machine | Azure VM | EC2 |
| Private network | VNet | VPC |
| Object storage | Blob Storage | S3 |
| Managed Kubernetes | AKS | EKS |
| Container registry | ACR | ECR |
| Serverless functions | Azure Functions | Lambda |
| Permissions for cloud resources | Azure RBAC | IAM |
| Application secrets | Key Vault | Secrets Manager |
| Metrics and logs | Azure Monitor | CloudWatch |
| Managed PostgreSQL | Azure Database for PostgreSQL | RDS for PostgreSQL |
| DNS | Azure DNS | Route 53 |
| Infrastructure templates | ARM/Bicep | CloudFormation |
| CI/CD (native DevOps tool) | Azure Pipelines | CodePipeline + CodeBuild (different design) |

---

## 6. End-to-End DevOps Architecture

**Scenario:** Deploy a containerized Python API to Azure Kubernetes Service using Azure DevOps.

```text
Developer
  |
  v
Azure Boards (work item)
  |
  v
Azure Repos (PR, branch policies)
  |
  v
Azure Pipelines: CI
  |-- Install dependencies and run pytest
  |-- Lint + SAST/SCA + container scan (configured tools)
  |-- docker build
  |-- docker push
  v
Azure Container Registry (ACR)
  |
  v
Azure Pipelines: CD
  |-- Obtain Azure access via service connection
  |-- Deploy image / Helm chart
  |-- Staging checks / production approval
  v
Azure Kubernetes Service (AKS)
  |-- Kubernetes Deployment + Service + Ingress
  |-- Key Vault-backed secrets (via supported identity integration)
  |-- VNet / NSG / load balancing
  v
Azure Monitor + Log Analytics + Managed Prometheus + Grafana
  |
  v
Alert / investigate / rollback / update Boards
```

### Roles of the components

- **Repos** stores code; **Pipelines** runs workflows.
- **ACR** stores built container images; **AKS** runs them.
- **Terraform** provisions infrastructure; **Helm/kubectl** manages Kubernetes workloads.
- **Key Vault** holds secrets; **Managed Identity / workload identity** avoids static credentials.
- **Azure Monitor** provides operational visibility.
- **Approvals** gate production deployments.

### Suggested project structure

```text
azure-devops-demo/
├── app/
│   └── main.py
├── tests/
│   └── test_app.py
├── Dockerfile
├── requirements.txt
├── azure-pipelines.yml
├── infra/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
└── README.md
```

---

## 7. Basic Azure CLI Commands

Install Azure CLI via [Microsoft's instructions](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli). Sign in with an authorized account and choose the correct subscription.

```bash
# Version and authentication
az version
az login
az account list --output table
az account set --subscription "<subscription-id-or-name>"
az account show --output table

# Read-only inventory
az group list --output table
az vm list --output table
az aks list --output table
az acr list --output table
az storage account list --output table
az keyvault list --output table

# Example: create a resource group (billable services placed inside may incur costs)
az group create --name rg-azuredevops-lab --location centralindia

# Check its details
az group show --name rg-azuredevops-lab --output table

# Inspect available AKS clusters and ACR registries in the chosen subscription
az aks list --resource-group rg-azuredevops-lab --output table
az acr list --resource-group rg-azuredevops-lab --output table
```

**Cleanup warning:** `az group delete --name rg-azuredevops-lab` deletes the resource group **and all resources inside it**. Run it only after confirming there are no resources you need to keep.

### Practical Docker / Kubernetes checks

```bash
# View Kubernetes context and nodes (requires configured cluster access)
kubectl config current-context
kubectl get nodes
kubectl get deployments -A
kubectl get pods -A

# Helm release inventory
helm list -A
```

---

## 8. Sample Azure Pipelines YAML

The following is a **starter template** for a Python app with a Dockerfile and `pytest` tests, pushing the image to ACR and rolling out an existing AKS Deployment. It is not turnkey until the named resources, permissions, and application manifests are configured.

**Prerequisites:**

1. Azure DevOps project with a Git repo containing a `Dockerfile`, `requirements.txt`, and `tests/`.
2. Existing ACR registry and AKS cluster; AKS can pull the private ACR image through appropriately configured identity/permissions.
3. An authorized **Docker Registry** service connection named `sc-acr-demo` pointing to ACR.
4. An authorized **Azure Resource Manager** service connection named `sc-azure-demo` with appropriate AKS access. Ensure the deployment identity is authorized under the cluster's Kubernetes/Azure RBAC configuration.
5. Existing Kubernetes Deployment named `sample-api` with container named `sample-api` in namespace `default` (or customize the command).
6. Hosted-agent capacity and any required organization permissions.

Save as `azure-pipelines.yml`:

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: ubuntu-latest

variables:
  acrLoginServer: 'mydemoacr.azurecr.io' # REPLACE with your registry login server
  imageRepository: 'sample-api'
  tag: '$(Build.BuildId)'
  resourceGroup: 'rg-azuredevops-lab'  # REPLACE
  aksCluster: 'aks-demo'              # REPLACE

stages:
  - stage: BuildAndTest
    displayName: 'Test and publish container'
    jobs:
      - job: Build
        steps:
          - checkout: self
          - task: UsePythonVersion@0
            inputs:
              versionSpec: '3.11'
          - script: |
              python -m pip install --upgrade pip
              pip install -r requirements.txt
              pip install pytest
              python -m pytest -q tests/
            displayName: 'Run unit tests'
          # Add linting, SAST/SCA, SBOM, and image-scanning steps here.
          - task: Docker@2
            displayName: 'Build and push image to ACR'
            inputs:
              command: 'buildAndPush'
              containerRegistry: 'sc-acr-demo'
              repository: '$(imageRepository)'
              Dockerfile: '$(Build.SourcesDirectory)/Dockerfile'
              tags: |
                $(tag)

  - stage: Deploy
    displayName: 'Deploy to AKS'
    dependsOn: BuildAndTest
    condition: succeeded()
    jobs:
      - deployment: DeployApp
        environment: 'dev' # Create this Azure DevOps Environment first
        strategy:
          runOnce:
            deploy:
              steps:
                - checkout: self
                - task: AzureCLI@2
                  displayName: 'Update AKS Deployment image'
                  inputs:
                    azureSubscription: 'sc-azure-demo'
                    scriptType: 'bash'
                    scriptLocation: 'inlineScript'
                    inlineScript: |
                      set -euo pipefail
                      az aks get-credentials \
                        --resource-group '$(resourceGroup)' \
                        --name '$(aksCluster)' \
                        --overwrite-existing
                      kubectl set image deployment/sample-api \
                        sample-api='$(acrLoginServer)/$(imageRepository):$(tag)' \
                        --namespace default
                      kubectl rollout status deployment/sample-api \
                        --namespace default --timeout=180s
```

### Pipeline flow

1. A push to `main` triggers the pipeline.
2. The hosted agent downloads the repo and runs Python tests.
3. `Docker@2` builds and pushes an image such as `mydemoacr.azurecr.io/sample-api:123`.
4. The deployment job authenticates to Azure through a service connection, selects the AKS cluster, and updates the existing Deployment.
5. `kubectl rollout status` checks the rollout. In production, also add canary/blue-green strategy, verification, rollback, gated approvals and secure secret delivery.

**Security consideration:** Do not commit passwords, client secrets or kubeconfigs to the repo. Prefer workload identity federation for Azure service connections when supported. CI agents must have network access to the chosen AKS API endpoint; private clusters may require self-hosted agents within an accessible network.

---

## 9. Hands-On Labs and Learning Roadmap

| Phase | Practical exercise | Skills demonstrated |
|---|---|---|
| **1. Azure basics** | Sign in, select subscription, create resource group, view resource inventory | Portal, Azure CLI, access scopes |
| **2. Compute and networking** | Create Linux VM, VNet, subnet, NSG; connect securely and install a small app | VM, network, SSH, RBAC |
| **3. Repos and Boards** | Create Azure DevOps project, Git repo, work item, PR and branch protection | Software delivery process |
| **4. Pipelines CI** | Create YAML build pipeline, run unit tests, publish pipeline artifact | CI, agent pools, artifacts |
| **5. Container registry** | Build image in pipeline and push to ACR | Docker, ACR, service connections |
| **6. Kubernetes CD** | Deploy image to AKS using Helm or Kubernetes manifests | AKS, release process, rollbacks |
| **7. Infrastructure as Code** | Provision resource group, VNet and ACR using Terraform | Terraform state, plan/apply |
| **8. Security** | Integrate Key Vault, managed identity, RBAC and vulnerability scanning | DevSecOps |
| **9. SRE** | Create dashboards and alerts, simulate a failing deployment, troubleshoot | Logs, metrics, reliability |
| **10. Production readiness** | Add staging/prod approvals, release tags, rollback and cost cleanup | Governance and release engineering |

**Recommended focus for DevOps interviews:** Azure Pipelines → service connections → self-hosted agents → Azure CLI → VNet/NSG → ACR → AKS → Terraform → Key Vault → Monitor.

**Cost warning:** Azure resources can incur charges, particularly AKS worker nodes, VMs, monitoring ingestion and private networking. Set budgets, review the pricing calculator, and clean up labs. Creating an empty resource group itself does not normally incur a charge.

---

## 10. Azure DevOps Interview Questions

**Q1. Azure vs. Azure DevOps?**  
Azure hosts cloud infrastructure and services; Azure DevOps provides work tracking, source management, CI/CD, testing and package management.

**Q2. What are the five core Azure DevOps services?**  
Boards, Repos, Pipelines, Artifacts, Test Plans.

**Q3. What is a YAML pipeline?**  
Pipeline configuration stored as code in a repo, defining triggers, stages, jobs, steps and reusable templates.

**Q4. Stage vs. job vs. step?**  
A stage groups a major phase; a job runs on an agent or server context; a step executes a task or script within a job.

**Q5. Microsoft-hosted vs. self-hosted agent?**  
Microsoft-hosted agents are managed temporary workers; self-hosted agents are machines you manage, useful for private-network access or specialized dependencies.

**Q6. What is a service connection?**  
A configured and permission-controlled method for a pipeline to authenticate to a target such as Azure, ACR or Kubernetes.

**Q7. What is the difference between pipeline artifacts and Azure Artifacts?**  
Pipeline artifacts store run outputs; Azure Artifacts manages versioned package feeds for supported package types.

**Q8. How do you protect secrets in pipelines?**  
Use Key Vault, secret variables/variable groups, least privilege, protected resources and preferably federated credentials; never put plain-text secrets in YAML.

**Q9. Why use AKS?**  
To run containerized workloads under managed Kubernetes with scheduling, rolling updates, health checks, scaling and ecosystem integrations.

**Q10. ACR vs. AKS?**  
ACR stores images; AKS runs container workloads using those images.

**Q11. VM vs. App Service vs. AKS?**  
VMs give OS-level control; App Service abstracts much of the platform for web apps; AKS gives Kubernetes orchestration and flexibility with more operational responsibilities.

**Q12. What is Azure RBAC?**  
Role-based authorization that grants identities specific actions on Azure resources at scoped levels.

**Q13. What is the difference between NSG and Azure Firewall?**  
NSGs provide subnet/NIC-level traffic filtering; Azure Firewall provides centralized, more advanced network filtering and policy management.

**Q14. What is a private endpoint?**  
A private IP interface in a VNet used to privately reach a supported service over Azure Private Link.

**Q15. How do you monitor AKS?**  
Use Azure Monitor, Container insights/log data, managed Prometheus, Grafana and alerts, according to the telemetry needed.

**Q16. How do you handle failed production deployment?**  
Stop or mitigate impact, investigate rollout and health signals, roll back to a known-good version, verify recovery, and run a blameless postmortem.

**Q17. Terraform vs. Bicep?**  
Terraform is provider-based and multi-cloud; Bicep is Azure-native IaC that compiles to ARM templates.

**Q18. How do you enforce CI security checks?**  
Use PR build validation, permissions, required pipeline checks, SAST/SCA, container scanning, SBOM and signed/trusted deployment artifacts where appropriate.

**Q19. How can pipelines reach a private AKS API?**  
Use an agent with appropriate routed/private connectivity and cluster permissions, typically a self-hosted agent in a reachable network.

**Q20. What should you log during a release?**  
Commit SHA, build/run ID, artifact or image digest, environment, deployment time, change approval, test results, rollout status and rollback reference.

---

## 11. Security, Reliability and Cost Checklist

- [ ] Enforce PR reviews and branch policies on important branches.
- [ ] Limit pipeline access to service connections and environments.
- [ ] Prefer federated identities / managed identities instead of static credentials.
- [ ] Store secrets in Azure Key Vault and rotate them appropriately.
- [ ] Scan dependencies, application code and container images.
- [ ] Pin production deployments to immutable artifact versions or digests.
- [ ] Set resource requests/limits, liveness/readiness probes and safe rollout settings in AKS.
- [ ] Configure alerts based on meaningful symptoms and SLOs.
- [ ] Protect infrastructure state files and use reviewed Terraform plans.
- [ ] Tag cloud resources and set budgets and retention settings.
- [ ] Have tested backup/restore and rollback procedures.
- [ ] Shut down or delete unnecessary training resources after validation.

---

## 12. Official Documentation

- [Azure Products and Services](https://azure.microsoft.com/en-us/products/)
- [Azure Documentation](https://learn.microsoft.com/en-us/azure/)
- [Azure DevOps Documentation](https://learn.microsoft.com/en-us/azure/devops/)
- [Azure DevOps — Get Started](https://learn.microsoft.com/en-us/azure/devops/get-started/)
- [Azure Pipelines YAML Schema](https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/)
- [Azure Pipelines Agents](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/agents)
- [Azure CLI Reference](https://learn.microsoft.com/en-us/cli/azure/)
- [Azure Kubernetes Service Documentation](https://learn.microsoft.com/en-us/azure/aks/)
- [Azure Container Registry Documentation](https://learn.microsoft.com/en-us/azure/container-registry/)
- [Azure Monitor Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/)
- [Azure Networking Overview](https://learn.microsoft.com/en-us/azure/networking/fundamentals/networking-overview)
- [Azure Managed Redis Migration / Azure Cache for Redis Changes](https://learn.microsoft.com/en-us/azure/azure-cache-for-redis/cache-whats-new)

---

**One-line summary:** **Boards** plans → **Repos** stores code → **Pipelines** builds/tests/deploys → **ACR** stores container images → **AKS** runs applications → **Monitor** measures health → **Terraform** provisions infrastructure → **Key Vault + Entra ID/RBAC** secure access.
