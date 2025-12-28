# azure-networking-basics-cli
Mini Azure Project – Build Virtual Network & Subnets via Azure CLI

This mini-lab demonstrates how to build Azure networking fundamentals purely via Azure CLI, without using the Azure Portal.

It is part of my AZ-104 learning path and serves as the foundation for later projects involving NSGs, Load Balancers, VM Scale Sets and Bastion.

---

## Goals
- Create a Resource Group
- Create an Azure Virtual Network (/16)
- Create multiple Subnets (/24)
- Deploy Virtual Machines into specific subnets
- Understand Azure IP planning and network segmentation
- Work CLI-only (no Portal)

---

## Prerequisites
- Azure Subscription
- Azure CLI or Azure Cloud Shell
- Basic understanding of IP addressing & CIDR

---

## Section 1 - Build Virtual Netzwork & Subnets and deploy Virtual Machines into specific Subnets

---

# Step 1 - Create Resource Group
```bash
az group create \
  --name rg-networking \
  --location westeurope \
  --output table
```

---

# Step 2 - Create Virtual Network
```bash
az network vnet create \
  --resource-group rg-networking \
  --name vnet-main \
  --address-prefix 10.0.0.0/16 \
  --location westeurope \
  --output table
```
Why /16?
- Large address space for future growth (subnets, scaling, services).
- Best practice in Azure to avoid early re-addressing.

---

# Step 3 Create Subnets
Subnet 1 - Frontend
```bash
az network vnet subnet create \
  --resource-group rg-networking \
  --vnet-name vnet-main \
  --name subnet-frontend \
  --address-prefix 10.0.1.0/24
``` 

---
Subnet 2 - Backend
```bash
az network vnet subnet create \
  --resource-group rg-networking \
  --vnet-name vnet-main \
  --name subnet-backend \
  --address-prefix 10.0.2.0/24
```
Why /24?
- 256 IPs per Subnet
- Azure reserves 5 -> 251 usable IPs
- Clean separation & easy scaling

---

# Step 4 - Deploy VM into Frontend Subnet
```bash
az vm create \
  --resource-group rg-networking \
  --name vm-frontend01 \
  --image Ubuntu2204 \
  --size Standard_B2s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --vnet-name vnet-main \
  --subnet subnet-frontend \
  --public-ip-sku Standard \
  --output table
```

---

# Step 5 Deploy VM into Backend Subnet
```
az vm create \
  --resource-group rg-networking \
  --name vm-backend01 \
  --image Ubuntu2204 \
  --size Standard_B2s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --vnet-name vnet-main \
  --subnet subnet-backend \
  --public-ip-sku Standard \
  --output table
```

---

# Step 6 - Verify Network Placement
```bash
az vm list-ip-addresses \
  --resource-group rg-networking \
  --output table
```

**Proof - Verify Placement**

<img width="482" height="76" alt="Screenshot 2025-12-28 185325" src="https://github.com/user-attachments/assets/5ea69e9b-d562-4de7-ac5e-4976817b811f" />


---

Cleanup (optional)
```bash
az group delete \
  --name rg-networking \
  --yes \
  --no-wait
```

---

## Learnings

- Azure IP planning (/16 VNet + /24 subnets)
- CLI-based network creation
- Subnet isolation & design
- Deploying VMs into specific subnets

- Foundation for:
- NSGs
- Bastion
- Load Balancers
- VM Scale Sets

## Next Steps

- Add Network Security Groups (NSG)
- Restrict traffic between subnets
- Remove public IP from backend VM
- Introduce Azure Bastion
- Combine with VMSS & Load Balancer







            
