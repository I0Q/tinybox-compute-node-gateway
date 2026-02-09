# tinybox-compute-node-gateway

VPC-only gateway proxy running on the DigitalOcean droplet `sf-cloud-1`.

Purpose:
- Accept requests from the StoryForge App Platform service.
- Authenticate them with a **gateway token**.
- Forward `/v1/*` calls to the Tinybox compute provider over Tailscale.

## Runtime

- Live path (droplet): `/opt/tinybox-compute-node-gateway`
- Service: `tinybox-compute-node-gateway.service`
- Listens (VPC-only): `http://10.108.0.3:8791`
- Forwards to Tinybox: `http://100.75.30.1:8790`

Tokens (on droplet):
- Gateway auth token file: `/root/.config/storyforge-cloud/gateway_token`
- Tinybox bearer token file: `/root/.config/storyforge-cloud/tinybox_token`

## Deploy/update (sf-cloud-1)

```bash
cd /opt/tinybox-compute-node-gateway
sudo git fetch --all
sudo git reset --hard origin/main
sudo /opt/tinybox-compute-node-gateway/.venv/bin/pip install -r requirements.txt
sudo systemctl restart tinybox-compute-node-gateway
sudo systemctl status tinybox-compute-node-gateway --no-pager
```

## Notes

- This gateway is intentionally simple: it proxies `/v1/{path}` and returns raw body/status/content-type.
- It does **not** expose any public listener; it binds to the droplet VPC interface.
