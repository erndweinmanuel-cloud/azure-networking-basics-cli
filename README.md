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


            
