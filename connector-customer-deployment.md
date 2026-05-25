<p align="center">
  <img src="https://devantisolutions.com/Devanti_Solutions_logo.png" width="220">
</p>

GATEWAY_URL=https://access.devantisolutions.com:9443
GATEWAY_WS_URL=wss://access.devantisolutions.com:9443/api/v1/connectors/ws
CONTROL_PLANE_URL=https://access.devantisolutions.com:9443
CONNECTOR_ID=c_bangalore-lab-new_wcg4nq
CONNECTOR_ORG_ID=1
CONNECTOR_TLS_MODE=strict
CONNECTOR_SERVER_NAME=access.devantisolutions.com
```

Use `CONNECTOR_MTLS_ENABLED=false` only for first registration when no
connector-specific client cert/key exists yet. After successful enrollment, the
connector writes the issued cert files and enables mTLS for the tunnel.

Do not point `CONTROL_PLANE_URL` at the admin/user portal hostname for connector
registration. Use the connector endpoint, normally the same value as
`GATEWAY_URL`: `https://access.devantisolutions.com:9443`.

## Public Docker Hub Image

Customer connector deployments use the public Docker Hub image:

```bash
docker pull devantisolutions/secureaccess-connector:latest
```

Minimal Docker run example:

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

For a full appliance-style install, the host must also have the Devanti helper
scripts and mount them into the container:

```text
/usr/local/sbin/devanti-connector-setup
/usr/local/sbin/devanti-connector-update
```

These scripts are not downloaded automatically by `docker run`. They are
installed by the OVA build or by the customer-side installer from the connector
deployment repository. Without `devanti-connector-update` mounted into the
container, the local UI will show `Updater missing`.

Required appliance mounts:

```text
-v /etc/devanti/connector:/etc/devanti/connector:rw
-v /etc/ssl/certs:/etc/ssl/certs:ro
-v /etc/netplan:/etc/netplan:rw
-v /usr/local/sbin/devanti-connector-update:/usr/local/sbin/devanti-connector-update:ro
-v /var/run/docker.sock:/var/run/docker.sock
```

The local UI is served by the connector process itself over HTTPS on TCP 443.
It calls local endpoints such as `/api/appliance`, `/api/status`,
`/api/logs`, and `/api/update/check`; those endpoints read/write local files
under `/etc/devanti/connector` and run the host-mounted update command.

Internal Harbor images such as `repo.devantisolutions.com/ztna/connector:latest` and lab images such as `repo.access.arraypmlab.in/ztna/connector:latest` are for internal Devanti validation only, not public customer defaults.

## Local Connector UI Repository Fields

In the HTTPS connector UI, public customer deployments should use:

```text
Repository server: docker.io
Connector image: devantisolutions/secureaccess-connector:latest
```

Use this exact image value:

```text
devantisolutions/secureaccess-connector:latest
```

Current connector builds normalize older default registry values such as `repo.devantisolutions.com/ztna/connector:latest` and `repo.access.arraypmlab.in/ztna/connector:latest` to the Docker Hub public default. If an upgraded appliance still shows:

```text
Repository: repo.devantisolutions.com
Image: repo.devantisolutions.com/ztna/connector:latest
```

then the VM is probably still using an older systemd unit that hardcoded the lab/private image. Install the current `devanti-connector.service`, run `sudo systemctl daemon-reload`, and restart `devanti-connector`. The current unit honors `CONNECTOR_IMAGE`, normalizes legacy defaults, and starts the container with `devantisolutions/secureaccess-connector:latest` unless a custom image is intentionally configured.

## Installer Template

This repository includes a customer-side installer template:

```bash
sudo ./scripts/install-connector.sh \
  --image devantisolutions/secureaccess-connector:latest \
  --gateway-url https://access.devantisolutions.com:9443 \
  --connector-id c_bangalore-lab-new_wcg4nq \
  --token '<connector-token>' \
  --config-dir /etc/devanti/connector \
  --systemd
```

The script writes `config.yaml`, `connector.env`, and optionally a systemd
service. It prints the generated token-only `ExecStart` so the operator can
review it before enabling the service.

Enable after review:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now devanti-connector
sudo journalctl -u devanti-connector -f
```

Use the Connector OVA setup flow when deploying as a virtual appliance. The OVA
console asks for network settings, Gateway URL, and connector token.

## Validation

On connector:

```bash
systemctl status devanti-connector
journalctl -u devanti-connector -f
curl -vk https://secureaccess.devantisolutions.com/access/health
curl -vk https://access.devantisolutions.com:9443
docker pull devantisolutions/secureaccess-connector:latest
```

Expected logs:

- connector registered
- connector websocket auth success
- connector tunnel connected

In Admin Portal:

- connector heartbeat status online
- last_seen updates
- gateway tunnel active
- mapped HTTP/HTTPS/RDP/SSH resources launch through that connector
  
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
