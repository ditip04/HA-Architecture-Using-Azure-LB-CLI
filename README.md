Highly Available Architecture Using Azure Load Balancer & CLI
1. Introduction -
Client aims to deploy a mission critical web application in a highly available, secure, and scalable environment on Microsoft Azure. To ensure uninterrupted service delivery, the organisation requires an architecture where only healthy virtual machine instances serve incoming traffic.
To achieve this, the operations team has chosen to implement an Azure Public Load Balancer and define the entire infrastructure using the Azure Command-Line Interface (CLI). This approach ensures repeatability, automation readiness, and eliminates manual deployment errors.
This project demonstrates the complete setup of a load balanced web application environment using Azure CLI, including networking, virtual machines, load balancer configuration, health probes, and secure access via Azure Bastion.
2. Architecture Overview -
The solution consists of the following components:
•	Virtual Network (VNet) with segregated subnets:
o	Application Subnet for backend VMs
o	AzureBastionSubnet for secure administrative access
•	Two Virtual Machines hosting the web application
•	Azure Public Load Balancer distributing traffic across healthy VM instances
•	Backend Pool containing VM NICs
•	Frontend Public IP for external access
•	Health Probe monitoring VM availability
•	Load Balancing Rule directing HTTP traffic
•	Azure Bastion Host enabling secure VM access without public IP exposure
•	Network Security Group (NSG) enforcing LB only inbound traffic
This architecture ensures that:
•	Only healthy VMs serve traffic
•	VMs remain isolated from the public internet
•	Administrative access is secure
•	Deployment is fully automated using CLI commands
3. Implementation Using Azure CLI -
3.1 Resource Group & Networking Setup -
A dedicated resource group and virtual network are created to logically isolate all components. Two subnets are defined: one for application VMs and one reserved for Azure Bastion.
3.2 Virtual Machine Deployment -
Two Linux-based virtual machines are deployed without public IPs to ensure security. A simple NGINX web server is installed on each VM to simulate the application workload.
3.3 Load Balancer Configuration -
A Standard SKU Public Load Balancer is deployed with:
•	Frontend IP Configuration
•	Backend Pool
•	Health Probe
•	Load Balancing Rule (TCP/80)
VM NICs are added to the backend pool, enabling the load balancer to distribute traffic across them.
3.4 Health Probe Implementation -
A health probe is configured to check the root path (/) on port 80. If a VM becomes unhealthy (e.g., NGINX stops), the load balancer automatically removes it from rotation.
3.5 Bastion Host Deployment -
Azure Bastion is deployed to provide secure SSH access to VMs without exposing them to the internet. This ensures compliance with Client’ security policies.
3.6 Network Security Hardening -
An NSG is applied to the application subnet to:
•	Allow HTTP traffic only from the load balancer
•	Deny all direct inbound internet traffic This enforces the rule that all user communication must pass through the load balancer.
4. High Availability Strategy -
The architecture ensures high availability through:
Load Balancing -
Traffic is evenly distributed across multiple VM instances. If one VM fails, the load balancer automatically routes traffic to healthy nodes.
Health Monitoring -
The health probe continuously checks VM responsiveness. Unhealthy VMs are removed from the backend pool until they recover.
Redundancy -
Multiple VMs ensure that the application remains available even during maintenance or unexpected failures.
Secure Access -
Azure Bastion provides secure administrative access without exposing VM public IPs, reducing attack surface.
5. Automation & Reusability -
All resources are deployed using Azure CLI commands, making the solution:
•	Reusable for future deployments
•	Consistent across environments
•	Version-controllable via scripts
This aligns with Client’ goal of adopting infrastructure-as-code principles.
6. Conclusion -
This project successfully demonstrates the deployment of a highly available, secure, and load balanced web application architecture using Azure CLI.
By leveraging:
•	Azure Load Balancer
•	Health Probes
•	Bastion Host
•	NSG-based security
•	CLI-driven automation
Client now has a fully repeatable and scalable deployment model suitable for production workloads.
The environment ensures that only healthy VM instances serve traffic, guaranteeing minimal downtime and optimal user experience.

