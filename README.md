
---

# Updated GitHub README.md (Copy-Paste Version)

```markdown
<p align="center">
  <img src="https://devantisolutions.com/Devanti_Solutions_logo.png" width="220">
</p>

# Devanti SecureAccess Connector Deployment

Deployment and onboarding guide for the Devanti SecureAccess Connector.

---

# Overview

The Devanti SecureAccess Connector is a lightweight outbound-only secure connector used to establish encrypted tunnels between customer environments and the Devanti SecureAccess Zero Trust platform.

The connector enables secure access to private applications without opening inbound firewall ports.

New dynamic connector deployments use token-authenticated onboarding with secure WSS connectivity to the Devanti Gateway.

Legacy mTLS connector deployments remain supported.

---

# Supported Deployment Models

- VMware OVA Appliance
- Docker Deployment
- Ubuntu VM Deployment
- Bare-metal Linux Deployment
- MSP / MSSP Multi-site Deployment

---

# Minimum System Requirements

## Recommended VM Resources

| Component | Requirement |
|---|---|
| OS | Ubuntu Server 22.04 LTS |
| vCPU | 2 |
| RAM | 4 GB |
| Storage | 40 GB |
| Network | 1 NIC |
| Internet | Required |

---

# Supported Platforms

- VMware ESXi
- Proxmox
- Hyper-V
- KVM
- Nutanix AHV
- AWS EC2
- Azure VM
- Google Cloud VM
- Bare-metal Ubuntu Server

---

# Customer Connector Flow

1. Admin creates a connector record.
2. Admin generates onboarding token.
3. Customer deploys Connector OVA or Docker container.
4. Customer configures:
   - Static IP
   - DNS
   - Gateway URL
   - Connector Token
5. Connector registers with Devanti Control Plane.
6. Connector establishes secure WSS tunnel to Gateway.
7. Admin verifies connector online status.

---

# Network Requirements

The connector requires outbound internet access only.

## Required Outbound Firewall Rules

| Protocol | Port | Destination |
|---|---|---|
| TCP | 9443 | access.devantisolutions.com |
| TCP | 443 | hub.docker.com |
| TCP | 443 | auth.docker.io |
| TCP | 443 | production.cloudflare.docker.com |

No inbound firewall ports from Devanti are required.

The local connector HTTPS management UI listens only on TCP 443 inside the customer management network.

---

# Install Docker

## Update the System

```bash
sudo apt update && sudo apt upgrade -y
```

---
## Product Website

https://devantisolutions.com/devanti-secure-access

---

## Company
Devanti Solutions  
Smart Infra Intelligence

https://devantisolutions.com


