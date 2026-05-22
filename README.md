<p align="center">
  <img src="https://devantisolutions.com/Devanti_Solutions_logo.png" width="220">
</p>

# Devanti SecureAccess Connector Deployment

Deployment and onboarding guide for the Devanti SecureAccess Connector.

---

# Overview

The Devanti SecureAccess Connector is a lightweight outbound-only secure connector used to establish encrypted tunnels between customer environments and the Devanti SecureAccess Zero Trust platform.

The connector enables secure access to private applications without opening inbound firewall ports.

Supported deployment models:

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

# Network Requirements

The connector requires outbound internet access only.

## Required Outbound Firewall Rules

| Protocol | Port | Destination |
|---|---|---|
| TCP | 9443 | access.devantisolutions.com |
| TCP | 443 | repo.devantisolutions.com |

No inbound firewall ports from Devanti are required.

---

# Install Docker

Update the system:

```bash
sudo apt update && sudo apt upgrade -y
```

Install required packages:

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

Add Docker repository:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install Docker:

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Enable Docker:

```bash
sudo systemctl enable --now docker
```

Verify Docker:

```bash
docker version
```

---

# Pull Connector Image

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

---

# Create Connector Configuration Directory

```bash
sudo mkdir -p /etc/devanti/connector
```

---

# Run Connector Container

```bash
docker run -d \
--name devanti-connector \
--restart unless-stopped \
-p 443:443 \
-v /etc/devanti/connector:/etc/devanti/connector \
devantisolutions/secureaccess-connector:latest
```

---

# Access Local Management UI

Open browser:

```text
https://<connector-ip>
```

Accept the self-signed certificate warning during first login.

---

# Initial Configuration

Configure:

- Connector ID
- Connector Token
- Gateway URL
- Network Settings
- DNS
- Hostname

Save the configuration to establish the secure tunnel.

---

# Security Features

- Outbound-only secure communication
- mTLS encrypted tunnel
- HTTPS local management UI
- Identity-aware access
- Secure session handling
- Zero Trust architecture
- Role-based access control
- Secure connector updates

---

# Update Connector

Pull latest image:

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

Restart connector:

```bash
docker restart devanti-connector
```

---

# Troubleshooting

Check container status:

```bash
docker ps
```

Check logs:

```bash
docker logs devanti-connector --tail=100
```

Check connectivity:

```bash
curl -vk https://access.devantisolutions.com:9443
```

---

# OVA Deployment

For enterprise VMware deployments, Devanti Solutions also provides a prebuilt OVA connector appliance for simplified onboarding.

---

# SaaS Platform

Customers access applications securely through the Devanti SecureAccess cloud platform without deploying core infrastructure components.

Product Website:

https://devantisolutions.com/devanti-secure-access

---

# Company

## Devanti Solutions

Smart Infra Intelligence

https://devantisolutions.com

---

# Support

For deployment assistance, onboarding, or enterprise integration support:

https://devantisolutions.com/contact
