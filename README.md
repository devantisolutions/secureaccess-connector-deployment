# Devanti SecureAccess Connector Deployment

<p align="center">
  <img src="https://devantisolutions.com/Devanti_Solutions_logo.png" width="220">
</p>

<p align="center">
  <b>Deployment and onboarding guide for the Devanti SecureAccess Connector</b>
</p>

---

# Overview

The Devanti SecureAccess Connector is a lightweight outbound-only secure connector used to establish encrypted tunnels between customer/private environments and the Devanti SecureAccess Zero Trust platform.

The connector enables secure access to private applications without opening inbound firewall ports.

New dynamic connector deployments use token-authenticated onboarding with secure encrypted tunnel connectivity to the Devanti Gateway.

Legacy mTLS connector deployments remain supported for enterprise deployments.

---

# Key Features

* Outbound-only secure connectivity
* No inbound firewall exposure required
* Secure encrypted tunnel communication
* Token-authenticated onboarding
* Connector-specific identity validation
* HTTPS local management UI
* Docker-based deployment
* VMware OVA appliance support
* MSP / MSSP multi-site deployment support
* Identity-aware Zero Trust access
* Secure HTTP / HTTPS / RDP / SSH publishing
* Centralized cloud management
* Connector auto-update support

---

# Supported Deployment Models

* VMware OVA Appliance
* Docker Deployment
* Ubuntu VM Deployment
* Bare-metal Linux Deployment
* MSP / MSSP Multi-site Deployment
* Cloud VM Deployment

---

# Supported Platforms

* VMware ESXi
* Proxmox
* Hyper-V
* KVM
* Nutanix AHV
* AWS EC2
* Microsoft Azure VM
* Google Cloud VM
* Bare-metal Ubuntu Server

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

The connector is installed inside the customer/private network and establishes an outbound encrypted tunnel to the Devanti Gateway.

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
          | Secure Outbound Connection
          |
+---------v---------+
| Devanti Gateway   |
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

1. Admin creates a connector record.
2. Admin generates a one-time onboarding token.
3. Customer deploys Connector OVA or Docker container.
4. Customer configures:

   * Static IP
   * Subnet / CIDR
   * Default Gateway
   * DNS
   * Gateway URL
   * Connector Token

5. Connector securely registers with Devanti Control Plane.
6. Control Plane validates the onboarding token and establishes connector identity.
7. Connector establishes encrypted secure tunnel connectivity.
8. Admin verifies connector online status and active tunnel.

---

# Connector Certificate Lifecycle

## Certificate Components

| Certificate / Component | Purpose |
|---|---|
| Platform TLS Certificate | Secure platform communication |
| Connector Identity Certificate | Secure connector authentication |
| Local UI Certificate | HTTPS local management UI |

---

## Certificate Usage Flow

| Stage | Action |
|---|---|
| Initial Deployment | Connector starts using onboarding token |
| First Registration | Control Plane validates onboarding token |
| Certificate Issuance | Connector identity certificates are generated |
| Secure Storage | Certificates stored securely inside connector |
| Future Connections | Connector automatically uses secure authentication |
| Tunnel Establishment | Secure encrypted tunnel established |

---

# First Registration Flow

Expected first registration process:

1. Admin creates connector record.
2. Admin generates onboarding token.
3. Connector starts using onboarding configuration.
4. Connector registers securely with Control Plane.
5. Control Plane validates onboarding token and establishes connector identity.
6. Connector securely stores issued identity materials locally.
7. Connector automatically enables secure authentication for future connections.
8. Connector establishes encrypted secure tunnel connectivity.

---

# Network Requirements

The connector requires outbound internet access only.

## Required Outbound Firewall Rules

| Protocol | Port | Purpose |
|---|---|---|
| TCP | 443 | Secure platform connectivity |
| TCP | 443 | Secure encrypted tunnel communication |
| TCP | 443 | Docker image download |
| UDP/TCP | 53 | DNS resolution |
| UDP | 123 | NTP synchronization |

---

## Important Notes

| Item | Details |
|---|---|
| Inbound Firewall Rules | No inbound firewall rules from Devanti are required |
| Local Management UI | HTTPS UI accessible only from management network |
| Tunnel Direction | Outbound-only secure encrypted connectivity |
| Internet Requirement | Outbound internet connectivity required |

---

# Install Docker on Ubuntu

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

## Install Docker Engine

```bash
sudo apt update

sudo apt install -y \
docker-ce \
docker-ce-cli \
containerd.io \
docker-buildx-plugin \
docker-compose-plugin
```

---

## Enable Docker

```bash
sudo systemctl enable --now docker
```

---

## Verify Docker Installation

```bash
docker version
```

---

# Public Docker Hub Image

Customer deployments use the public Docker Hub image:

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

---

# Create Connector Configuration Directory

```bash
sudo mkdir -p /etc/devanti/connector
```

---

# First-Boot Runtime Configuration

## Example config.yaml

```yaml
connector_id: "connector-branch-office-01"

gateway_url: "https://<gateway-fqdn>"

gateway_ws_url: "wss://<gateway-fqdn>/<secure-websocket-endpoint>"

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

GATEWAY_URL=https://<gateway-fqdn>

GATEWAY_WS_URL=wss://<gateway-fqdn>/<secure-websocket-endpoint>

CONTROL_PLANE_URL=https://<gateway-fqdn>

CONNECTOR_ID=connector-branch-office-01

CONNECTOR_ORG_NAME="<organization-name>"

CONNECTOR_TLS_MODE=strict

CONNECTOR_SERVER_NAME=<gateway-fqdn>
```

---

# Important Notes

## Initial Registration

Use:

```text
CONNECTOR_MTLS_ENABLED=false
```

ONLY during first registration when connector identity certificates are not yet issued.

After successful enrollment, the connector automatically enables secure authentication for future connections.

---

## Connector Identity

Each connector maintains a unique secure identity.

Do NOT copy or reuse connector identity materials between multiple connectors.

Reusing identity materials may cause:

* Connector conflicts
* Offline status
* Tunnel authentication failures

---

## Control Plane URL

Use the dedicated secure gateway or platform hostname provided during onboarding.

Do not use unrelated management portal hostnames.

---

# Run Connector Container

## Recommended Production Deployment

```bash
docker run -d \
  --name devanti-connector \
  --restart unless-stopped \
  --env-file /etc/devanti/connector/connector.env \
  -p 443:443 \
  -v /etc/devanti/connector:/etc/devanti/connector:rw \
  -v /etc/ssl/certs:/etc/ssl/certs:ro \
  devantisolutions/secureaccess-connector:latest
```

---

# Local HTTPS Management UI

The connector includes a local HTTPS management UI.

Access:

```text
https://<connector-ip>
```

Accept the self-signed certificate warning during first login.

---

# Local UI Features

The local connector UI provides:

* Appliance configuration
* Connector onboarding
* Network settings
* DNS settings
* Gateway configuration
* Tunnel status
* Connector logs
* Update management
* Health diagnostics
* Error summaries
* Certificate status

---

# Local UI Repository Settings

Public customer deployments should use:

```text
Repository Server: docker.io
Connector Image: devantisolutions/secureaccess-connector:latest
```

Use this exact image:

```text
devantisolutions/secureaccess-connector:latest
```

---

# Installer Template

Example customer-side installer:

```bash
sudo ./scripts/install-connector.sh \
  --image devantisolutions/secureaccess-connector:latest \
  --gateway-url https://<gateway-fqdn> \
  --connector-id connector-branch-office-01 \
  --token '<connector-token>' \
  --config-dir /etc/devanti/connector \
  --systemd
```

---

# Enable Connector Service

```bash
sudo systemctl daemon-reload

sudo systemctl enable --now devanti-connector

sudo journalctl -u devanti-connector -f
```

---

# Validation

## Check Service Status

```bash
systemctl status devanti-connector
```

---

## Check Logs

```bash
journalctl -u devanti-connector -f
```

---

## Check Docker Container

```bash
docker ps
```

---

## Check Connector Logs

```bash
docker logs devanti-connector --tail=100
```

---

## Verify Docker Hub Pull

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

---

# Expected Successful Logs

Expected connector logs include:

* connector registered
* connector authentication successful
* connector tunnel connected

---

# Admin Portal Validation

Admin Portal should display:

* Connector online status
* Heartbeat updates
* Active tunnel status
* Last seen timestamp
* Connected gateway
* Accessible mapped resources

---

# OVA Deployment

For enterprise VMware deployments, Devanti Solutions also provides a prebuilt Connector OVA appliance for simplified onboarding.

The OVA deployment wizard includes:

* Static IP configuration
* DNS configuration
* Gateway URL configuration
* Connector token onboarding
* Automatic connector registration

---

# SaaS Platform

Customers securely access private applications through the Devanti SecureAccess cloud platform without exposing internal infrastructure to the internet.

---

# Product Website

https://devantisolutions.com/devanti-secure-access

---

# Company

Devanti Solutions  
Smart Infra Intelligence

---

# Support

https://devantisolutions.com/support

---

# License

Copyright © Devanti Solutions.  
All Rights Reserved.
