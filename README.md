# cloudflare-tunnel

Cloudflare Tunnel for internal services (n8n + optional Pi-hole) using the
`jmake.space` zone.

## Prereqs
- `cloudflared` CLI installed locally
- Domain `jmake.space` managed in Cloudflare
- Static/reserved IPs for internal services

## Create the tunnel
```bash
cloudflared tunnel login
cloudflared tunnel create k3s-tunnel
cloudflared tunnel route dns k3s-tunnel n8n.jmake.space
cloudflared tunnel route dns k3s-tunnel pihole.jmake.space
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
- n8n service is expected at `http://n8n.n8n.svc.cluster.local:5678`.
- Pi-hole admin is reachable at `https://pihole.jmake.space/admin`.
*** End Patch"}']}"}] }
