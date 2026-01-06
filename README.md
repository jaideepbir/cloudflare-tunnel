# cloudflare-tunnel

Cloudflare Tunnel for internal services (n8n) using the
`jmake.space` zone.

## Architecture
```
Cloudflare DNS -> Tunnel (k3s: cloudflare namespace)
  - n8n.jmake.space -> n8n.n8n.svc.cluster.local:80
  - photos.jmake.space -> 192.168.1.132:2283 (pi5d03)
  - wiki.jmake.space -> wiki-bookstack.wiki.svc.cluster.local:80
```

## Prereqs
- `cloudflared` CLI installed locally
- Domain `jmake.space` managed in Cloudflare
- Static/reserved IPs for internal services

## Create the tunnel
```bash
cloudflared tunnel login
cloudflared tunnel create k3s-tunnel
cloudflared tunnel route dns k3s-tunnel n8n.jmake.space
cloudflared tunnel route dns k3s-tunnel photos.jmake.space
cloudflared tunnel route dns k3s-tunnel wiki.jmake.space
```

## Configure the tunnel
1) Update `k8s/configmap.yaml` with the tunnel UUID and hostnames.
2) Create the credentials secret:
```bash
kubectl -n cloudflare create secret generic cloudflared-credentials \
  --from-file=credentials.json=$HOME/.cloudflared/<tunnel-uuid>.json
```

## Deploy
```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
```

## Notes
- Tunnel UUID: `ef31ed8e-866f-4872-969a-f6e3a44c269a`
- n8n service is expected at `http://n8n.n8n.svc.cluster.local:80`.
- Immich (photos) is proxied to `http://192.168.1.132:2283` (pi5d03).
- Add additional hostnames in `k8s/configmap.yaml` if you want more services.
*** End Patch"}']}"}] }
