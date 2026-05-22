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

## Install Required Packages

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

---

## Add Docker Repository

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

---

## Install Docker

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

---

## Enable Docker

```bash
sudo systemctl enable --now docker
```

---

## Verify Docker

```bash
docker version
```

---

# Public Docker Hub Image

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
connector_id: "c_bangalore-lab-new_wcg4nq"
gateway_url: "https://access.devantisolutions.com:9443"
gateway_ws_url: "wss://access.devantisolutions.com:9443/api/v1/connectors/ws"
log_level: "info"
reconnect_interval: "5s"

mtls:
  enabled: false

tls:
  mode: "strict"
```

---

## Example connector.env

```env
CONNECTOR_CONFIG_FILE=/etc/devanti/connector/config.yaml
CONNECTOR_MTLS_ENABLED=false
CONNECTOR_TOKEN=<connector-token>
GATEWAY_URL=https://access.devantisolutions.com:9443
GATEWAY_WS_URL=wss://access.devantisolutions.com:9443/api/v1/connectors/ws
CONNECTOR_ID=c_bangalore-lab-new_wcg4nq
CONNECTOR_TLS_MODE=strict
```

---

# Run Connector Container

```bash
docker run -d \
  --name devanti-connector \
  --restart unless-stopped \
  -p 443:443 \
  -v /etc/devanti/connector:/etc/devanti/connector:rw \
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

# Connector UI Repository Settings

Public customer deployments should use:

```text
Repository Server: docker.io
Connector Image: devantisolutions/secureaccess-connector:latest
```

---

# Initial Configuration

Configure:

- Connector ID
- Connector Token
- Gateway URL
- DNS
- Network Settings
- Hostname

Save configuration to establish the secure tunnel.

---

# Security Features

- Outbound-only secure communication
- Token-authenticated onboarding
- WSS encrypted tunnel
- Optional legacy mTLS support
- HTTPS local management UI
- Identity-aware access
- Secure session handling
- Zero Trust architecture
- Role-based access control

---

# Update Connector

## Pull Latest Image

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

---

## Restart Connector

```bash
docker restart devanti-connector
```

---

# Validation

## Check Container Status

```bash
docker ps
```

---

## Check Logs

```bash
docker logs devanti-connector --tail=100
```

---

## Check Gateway Connectivity

```bash
curl -vk https://access.devantisolutions.com:9443
```

---

## Verify Docker Hub Pull

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

---

# Expected Connector Logs

Expected successful logs include:

- connector registered
- connector websocket auth success
- connector tunnel connected

---

# Admin Portal Validation

Admin Portal should display:

- connector online status
- heartbeat updates
- active tunnel status
- accessible mapped resources

---

# OVA Deployment

For VMware enterprise deployments, Devanti Solutions also provides a prebuilt Connector OVA appliance for simplified onboarding.

---

# SaaS Platform

Customers securely access applications through the Devanti SecureAccess cloud platform without deploying core infrastructure components.

---

# Product Website

https://devantisolutions.com/devanti-secure-access

---

# Company

Devanti Solutions  
Smart Infra Intelligence

https://devantisolutions.com

---

# Support

For deployment assistance, onboarding, or enterprise integration support:

https://devantisolutions.com/support
