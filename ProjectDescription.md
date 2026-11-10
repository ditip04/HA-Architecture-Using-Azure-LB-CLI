# Highly Available Web Application Using Azure Load Balancer & Azure CLI

## 📌 Project Overview

This project demonstrates the deployment of a **highly available and secure web application infrastructure using Microsoft Azure Load Balancer and Azure Command-Line Interface (CLI)**.

The solution distributes incoming HTTP traffic across multiple Azure Virtual Machines and ensures that only healthy instances receive user requests through health probes.

The infrastructure is deployed using Azure CLI to support automation, consistency, repeatability and infrastructure-as-code practices.

The project was completed as part of the **Microsoft Azure AZ-104: Microsoft Azure Administrator** training.

## 🎯 Project Objectives

- Design and deploy a highly available Azure infrastructure.
- Create and configure Azure Virtual Networks and subnets.
- Deploy multiple Linux-based Azure Virtual Machines.
- Configure an Azure Standard Public Load Balancer.
- Create and configure backend address pools and frontend IP configurations.
- Implement health probes to monitor backend VM availability.
- Configure load balancing rules to distribute HTTP traffic.
- Deploy Azure Bastion for secure administrative access.
- Configure Network Security Groups (NSGs) to restrict direct internet access to backend VMs.
- Automate infrastructure deployment using Azure CLI.

## 🏢 Business Scenario

Client requires a highly available environment to host its mission-critical web application on Microsoft Azure.

The organisation wants to ensure that incoming user requests are distributed across multiple virtual machine instances and that unhealthy instances are automatically excluded from serving traffic.

The solution must satisfy the following requirements:

**High Availability**
- Deploy multiple virtual machines to host the web application.
- Distribute incoming traffic across healthy backend instances.
- Automatically detect unhealthy virtual machines using health probes.

**Security**
- Prevent direct public internet access to backend virtual machines.
- Use private IP addresses for backend VMs.
- Provide secure administrative access through Azure Bastion.
- Restrict inbound traffic using Network Security Groups.

**Automation**
- Deploy infrastructure using Azure CLI commands.
- Ensure consistent and repeatable resource provisioning.
- Support future infrastructure automation and deployment.

## 🏗️ Solution Architecture

The solution consists of the following Azure components:

| Azure Component | Purpose |
|---|---|
| Azure Resource Group | Logical grouping of project resources |
| Azure Virtual Network | Provides private network connectivity |
| Application Subnet | Hosts backend application VMs |
| AzureBastionSubnet | Dedicated subnet for Azure Bastion |
| Azure Virtual Machines | Hosts the NGINX web application |
| Azure Public Load Balancer | Distributes incoming HTTP traffic |
| Frontend Public IP | Provides external access to the application |
| Backend Pool | Contains backend VM network interfaces |
| Health Probe | Monitors backend VM availability |
| Load Balancing Rule | Distributes HTTP traffic across backend VMs |
| Azure Bastion | Provides secure SSH access to private VMs |
| Network Security Group | Controls and restricts network traffic |

### Architecture Diagram

```text
                  ┌──────────────────────────┐
                  │      Internet Users      │
                  └────────────┬─────────────┘
                               │
                       Public Frontend IP
                               │
                  ┌────────────▼─────────────┐
                  │   Azure Public Load      │
                  │        Balancer          │
                  └────────────┬─────────────┘
                               │
                    HTTP Traffic (TCP/80)
                               │
                     Health Probe (HTTP)
                               │
             ┌─────────────────┴─────────────────┐
             │                                   │
    ┌────────▼────────┐                 ┌────────▼────────┐
    │   VM-App-01     │                 │   VM-App-02     │
    │   NGINX Web     │                 │   NGINX Web     │
    │   Server        │                 │   Server        │
    └─────────────────┘                 └─────────────────┘
             │                                   │
             └─────────────────┬─────────────────┘
                               │
                    Application Subnet
                               │
                  ┌────────────▼─────────────┐
                  │      Azure Bastion       │
                  │   Secure SSH Access      │
                  └──────────────────────────┘
```

## ⚙️ Implementation Using Azure CLI

All infrastructure components were provisioned using Azure CLI commands to demonstrate automated cloud resource deployment.

### Step 1: Create Resource Group and Networking

Created a dedicated resource group to logically isolate the project infrastructure.

Configured an Azure Virtual Network with two separate subnets:

**Application Subnet**
- Hosts the backend application virtual machines.
- Provides private network connectivity.
- Applies NSG rules to restrict inbound traffic.

**Azure Bastion Subnet**
- Dedicated subnet for Azure Bastion.
- Enables secure administrative access to private virtual machines.
- Prevents the need to expose VM public IP addresses.

### Step 2: Deploy Virtual Machines

Deployed two Linux-based virtual machines within the application subnet.

| Configuration | VM-App-01 | VM-App-02 |
|---|---|---|
| Operating System | Linux | Linux |
| Network | Azure VNet | Azure VNet |
| Subnet | Application Subnet | Application Subnet |
| Public IP | Not Assigned | Not Assigned |
| Web Server | NGINX | NGINX |
| Purpose | Backend Web Server | Backend Web Server |

**Application Configuration:**

- Installed NGINX on both virtual machines.
- Configured each VM to host a web application.
- Used private IP addresses for internal communication.
- Verified that both web servers were responding to HTTP requests.

### Step 3: Configure Azure Public Load Balancer

Deployed an Azure Standard SKU Public Load Balancer to distribute incoming internet traffic across backend virtual machines.

**Load Balancer Configuration**

The following components were configured:

**Frontend IP Configuration**
- Associated a public IP address with the load balancer.
- Enabled external access to the web application.

**Backend Address Pool**
- Created a backend pool for application workloads.
- Added the network interfaces of VM-App-01 and VM-App-02.

**Load Balancing Rule**
- Protocol: TCP
- Frontend Port: 80
- Backend Port: 80
- Purpose: Distribute incoming HTTP traffic across backend VMs.

### Step 4: Configure Health Probe

Created an HTTP health probe to continuously monitor the availability of backend virtual machines.

**Health Probe Configuration**

| Configuration | Value |
|---|---|
| Protocol | HTTP |
| Port | 80 |
| Request Path | / |
| Purpose | Monitor backend web server health |

**Health Monitoring Process:**

1. The load balancer periodically sends health probe requests to backend VMs.
2. Healthy VMs continue receiving incoming application traffic.
3. If a VM becomes unhealthy, such as when its NGINX service stops responding, it is removed from load-balancing rotation.
4. Once the VM recovers and passes health checks, it can receive traffic again.

This ensures that user requests are directed only to healthy backend instances.

### Step 5: Deploy Azure Bastion

Deployed Azure Bastion to provide secure administrative access to the backend virtual machines.

**Configuration:**
- Created the dedicated `AzureBastionSubnet`.
- Deployed Azure Bastion within the virtual network.
- Used Azure Portal Bastion connectivity to access the private VMs through SSH.
- Avoided assigning public IP addresses directly to backend VMs.

This approach reduces the attack surface and provides centralised administrative access.

### Step 6: Configure Network Security Groups (NSGs)

Configured NSG rules to restrict inbound access to backend application virtual machines.

**Security Configuration:**

- Allow HTTP traffic from the `AzureLoadBalancer` service tag to backend VMs.
- Restrict direct inbound internet traffic to backend VMs.
- Permit required administrative connectivity through Azure Bastion.
- Keep backend virtual machines accessible through private IP addresses only.

This ensures that application traffic follows the intended route through the public load balancer rather than accessing backend VMs directly.

## 🔄 High Availability Strategy

The architecture implements multiple mechanisms to improve application availability and reliability.

### 1. Load Balancing

Azure Public Load Balancer distributes incoming HTTP requests across multiple backend virtual machines.

This prevents application traffic from depending on a single server.

### 2. Health Monitoring

Health probes continuously monitor backend VM availability.

Unhealthy instances are automatically removed from traffic distribution until they recover.

### 3. Backend Redundancy

Two backend virtual machines provide redundancy and help maintain application availability if one instance becomes unavailable.

### 4. Secure Administrative Access

Azure Bastion enables secure SSH connectivity without exposing backend VMs directly to the public internet.

### 5. Network Security

NSGs enforce controlled inbound access and ensure that application traffic reaches the backend through the intended network path.

## 🤖 Automation and Reusability

Azure CLI was used to provision the infrastructure instead of relying entirely on manual deployment through the Azure Portal.

**Key Benefits:**

- **Automation:** Infrastructure can be deployed through command-line scripts.
- **Consistency:** Repeatable resource configurations across environments.
- **Reusability:** Commands can be adapted for future deployments.
- **Version Control:** Deployment scripts can be maintained in GitHub.
- **Reduced Manual Errors:** Standardised deployment procedures improve consistency.
- **Scalability:** The architecture can be extended with additional backend instances.

## 🧪 Testing and Validation

The deployed environment can be validated using the following tests:

| Test | Expected Result |
|---|---|
| Access Load Balancer Public IP | Web application loads successfully |
| Backend Pool Verification | Both VMs appear in the backend pool |
| Health Probe Status | Healthy backend instances show Healthy status |
| Stop NGINX on VM-App-01 | VM-App-01 becomes unhealthy |
| Access Application After Failure | VM-App-02 continues serving traffic |
| Restart NGINX on VM-App-01 | VM-App-01 returns to healthy status |
| Direct Public Access to VMs | Not available |
| Azure Bastion SSH Access | Secure VM access succeeds |

These tests demonstrate load balancing, health monitoring, backend redundancy and secure administrative access.

## 📊 Project Outcome

Successfully designed and deployed a highly available web application infrastructure using Azure Public Load Balancer and Azure CLI.

The implementation demonstrated the ability to:

- Deploy and configure Azure networking infrastructure.
- Provision Linux-based virtual machines.
- Configure Standard SKU Public Load Balancing.
- Create backend pools and frontend IP configurations.
- Implement HTTP health probes.
- Distribute incoming traffic across multiple backend instances.
- Deploy Azure Bastion for secure administrative access.
- Configure NSGs to restrict direct internet access.
- Automate infrastructure deployment using Azure CLI.

## 🧠 Technical Skills Demonstrated

- Microsoft Azure IaaS
- Azure CLI
- Azure Virtual Networks (VNet)
- Azure Subnets
- Azure Virtual Machines
- Azure Public Load Balancer
- Standard SKU Load Balancer
- Backend Address Pools
- Frontend IP Configuration
- Health Probes
- Load Balancing Rules
- Network Security Groups (NSGs)
- Azure Bastion
- Linux Administration
- NGINX Web Server
- High Availability Architecture
- Cloud Infrastructure Automation
- Network Security and Troubleshooting

## 🏁 Conclusion

This project successfully demonstrated the deployment of a highly available and secure web application architecture using Microsoft Azure.

By combining Azure Public Load Balancer, health probes, multiple backend virtual machines, Azure Bastion and NSG-based security, the solution provides a structured approach to distributing application traffic and maintaining service availability.

The use of Azure CLI demonstrates practical experience in cloud infrastructure automation, repeatable deployments and network configuration.

This project reflects real-world Azure infrastructure administration scenarios and provides a foundation for designing scalable and resilient cloud-hosted applications.
