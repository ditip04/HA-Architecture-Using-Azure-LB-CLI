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
2. Healthy VMs continue receiving incoming application
