# Devanti SecureAccess Connector Deployment

<p align="center">
  <img src="https://devantisolutions.com/Devanti_Solutions_logo.png" width="220">
</p>

<p align="center">
  <b>Deployment and onboarding guide for the Devanti SecureAccess Connector</b>
</p>

---

# Overview

The Devanti SecureAccess Connector is a lightweight outbound-only secure connector used to establish encrypted tunnels between customer/private environments and the Devanti SecureAccess platform.

The Connector enables secure access to private applications without exposing inbound firewall ports to the internet.

The Connector supports:

- Secure outbound-only connectivity
- Encrypted tunnel communication
- Web application publishing
- SSH access
- RDP access
- Identity-aware Zero Trust access
- Centralized cloud management
- Multi-site deployments
- Automatic reconnect and health monitoring

---

# Key Features

- Outbound-only secure connectivity
- No inbound firewall exposure required
- Secure encrypted communication
- Token-authenticated onboarding
- Automatic certificate lifecycle management
- HTTPS local management interface
- Docker deployment support
- VMware OVA appliance support
- MSP / MSSP deployment support
- Centralized cloud management
- Connector health monitoring
- Controlled update workflow

---

# Supported Deployment Models

- VMware OVA Appliance
- Docker Deployment
- Ubuntu VM Deployment
- Bare-metal Linux Deployment
- Cloud VM Deployment
- MSP / MSSP Multi-site Deployment

---

# Supported Platforms

- VMware ESXi
- Proxmox
- Hyper-V
- KVM
- Nutanix AHV
- AWS EC2
- Microsoft Azure VM
- Google Cloud VM
- Bare-metal Ubuntu Server

---

# Minimum System Requirements

## Recommended VM Resources

| Category | Details |
|---|---|
| Operating System | Ubuntu Server 22.04 LTS |
| vCPU | 2 |
| Memory (RAM) | 4 GB |
| Storage | 40 GB |
| Network Interface | 1 NIC |
| Internet Connectivity | Required |

---

## Recommended Production Sizing

| Environment Type | Recommended vCPU | Recommended RAM | Recommended Storage |
|---|---|---|---|
| Small Lab / POC | 2 vCPU | 4 GB | 40 GB |
| Medium Production | 4 vCPU | 8 GB | 80 GB |
| Large Enterprise | 8+ vCPU | 16+ GB | 160+ GB |

---

# Architecture Overview

The Connector is deployed inside the customer/private network and establishes a secure outbound connection to the Devanti SecureAccess platform.

No inbound firewall ports are required from the internet to the customer environment.

```text
+-------------------+
| Customer Network  |
| Private Apps      |
+---------+---------+
          |
          |
+---------v---------+
| Devanti Connector |
| Secure Tunnel     |
+---------+---------+
          |
          |
+---------v---------+
| Devanti Platform  |
| Zero Trust Access |
+---------+---------+
          |
          |
+---------v---------+
| End Users         |
| Browser / RDP     |
| SSH / HTTPS       |
+-------------------+
```

---

# Customer Connector Flow

1. Administrator creates a Connector record.
2. Administrator generates a one-time onboarding token.
3. Customer deploys Connector appliance or container.
4. Customer configures:
   - Network settings
   - DNS settings
   - Platform URL
   - Connector onboarding token
5. Connector securely registers with the platform.
6. Secure encrypted communication is established.
7. Administrator verifies connector online status.

---

# Security Architecture

## Security Features

The Connector includes enterprise-grade security controls:

- Outbound-only communication
- Encrypted communication
- Secure connector identity validation
- Automatic certificate lifecycle management
- Token-based onboarding
- Session isolation
- Secure tunnel communication
- Connector heartbeat monitoring
- No inbound internet exposure

---

# Network Requirements

The Connector requires outbound internet connectivity only.

## Required Outbound Firewall Rules

| Protocol | Port | Purpose |
|---|---|---|
| HTTPS | 443 | Platform communication |
| Secure Tunnel | 443 | Encrypted tunnel communication |
| DNS | 53 | DNS resolution |
| NTP | 123 | Time synchronization |

---

# Important Notes

| Item | Details |
|---|---|
| Inbound Firewall Rules | No inbound firewall rules are required |
| Tunnel Direction | Outbound-only encrypted communication |
| Internet Requirement | Outbound internet connectivity required |
| Local Management UI | Accessible only from internal management network |

---

# Install Docker on Ubuntu

## Update System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Install Required Packages

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

---

## Install Docker

Follow official Docker installation instructions:

https://docs.docker.com/engine/install/ubuntu/

---

## Verify Docker Installation

```bash
docker version
```

---

# Public Docker Image

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

---

# Create Connector Configuration Directory

```bash
sudo mkdir -p /etc/devanti/connector
```

---

# Example Runtime Configuration

## Example config.yaml

```yaml
connector_name: "branch-office-connector"

platform_url: "https://<platform-url>"

log_level: "info"

reconnect_interval: "5s"
```

---

## Example connector.env

```env
CONNECTOR_TOKEN=<onboarding-token>

PLATFORM_URL=https://<platform-url>

CONNECTOR_NAME=branch-office-connector
```

---

# Connector Identity

Each Connector maintains a unique identity.

Do not reuse Connector configuration or identity information across multiple deployments.

---

# Run Connector Container

## Recommended Deployment

```bash
docker run -d \
  --name devanti-connector \
  --restart unless-stopped \
  --env-file /etc/devanti/connector/connector.env \
  -p 443:443 \
  -v /etc/devanti/connector:/etc/devanti/connector:rw \
  devantisolutions/secureaccess-connector:latest
```

---

# Local HTTPS Management UI

The Connector includes a local HTTPS management interface.

Access:

```text
https://<connector-ip>
```

Accept the self-signed certificate warning during first login.

---

# Local UI Features

The local management interface provides:

- Appliance configuration
- Connector onboarding
- Network configuration
- DNS configuration
- Tunnel status
- Connector health status
- Update management
- System diagnostics
- Log visibility

---

# Validation

## Check Service Status

```bash
systemctl status devanti-connector
```

---

## Check Docker Container

```bash
docker ps
```

---

## Check Logs

```bash
docker logs devanti-connector --tail=100
```

---

# Expected Successful Status

Expected healthy status includes:

- Connector online
- Tunnel connected
- Heartbeat active

---

# Admin Portal Validation

The Admin Portal displays:

- Connector online status
- Heartbeat updates
- Tunnel status
- Last seen timestamp
- Assigned resources

---

# OVA Deployment

Devanti Solutions also provides a prebuilt Connector OVA appliance for simplified enterprise onboarding.

The OVA deployment wizard includes:

- Static IP configuration
- DNS configuration
- Platform URL configuration
- Connector onboarding
- Automatic registration workflow

---

# SaaS Platform

Customers securely access private applications through the Devanti SecureAccess platform without exposing internal infrastructure to the internet.

---

# Product Website

https://devantisolutions.com/devanti-secure-access

---

# Company

https://devantisolutions.com

Smart Infra Intelligence

---

# Support

https://devantisolutions.com/support

---

# License

Copyright © Devanti Solutions.
All Rights Reserved.
