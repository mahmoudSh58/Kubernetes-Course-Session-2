# Kubernetes Course — Session 2: Storage, Configuration & Networking

Teaching material extracted from the Session 2 slide deck.
Each topic has its own folder with ready-to-apply **YAML examples** and a **`commands.txt`**
that has every command for that topic in teaching order. The comments in each file give the slide numbers.

> Run all commands from the **repository root** (the paths look like `kubectl apply -f 01-storage/...`).
> Everything runs in the namespace `webapp-stack` on minikube.

## Folders (in session order)

| # | Folder | Topic | Slides | Files |
|---|--------|-------|--------|-------|
| 00 | [`00-setup`](00-setup) | Cluster, namespace, minikube cheat-sheet | 78 | `namespace.yaml` |
| 01 | [`01-storage`](01-storage) | emptyDir, PV / PVC, access modes, StorageClass, PV life cycle | 6–18, 68–69 | `writer-pod-emptydir.yaml`, `data-pv-pvc.yaml`, `data-pvc-2gi-pending.yaml`, `writer-pod.yaml`, `dynamic-pvc.yaml`, `storageclass-gp3-aws.yaml` |
| 02 | [`02-jobs-cronjobs`](02-jobs-cronjobs) | Jobs and CronJobs, cron syntax | 19–24, 70–71 | `seed-job.yaml`, `stamp-cronjob.yaml` |
| 03 | [`03-configmaps-secrets`](03-configmaps-secrets) | ConfigMap as env and as a file, Secrets and base64, encryption at rest | 25–33, 72 | `webapp-config.yaml`, `nginx-config-pod.yaml`, `mysql-secret.yaml`, `secret-env-pod.yaml`, `encryption-config.yaml` |
| 04 | [`04-networking-model`](04-networking-model) | Pod IPs, Pod-to-Pod traffic, CNI plugins | 34–36, 73 | `two-pods.yaml` |
| 05 | [`05-labels-selectors`](05-labels-selectors) | Labels, selectors, annotations | 38–40 | `label-demo-pods.yaml`, `webapp-api-deployment.yaml` |
| 06 | [`06-services`](06-services) | ClusterIP, NodePort, LoadBalancer, EndpointSlices, Headless, ExternalName | 41–49, 74–75 | `webapp-api-svc.yaml`, `web-nodeport-svc.yaml`, `web-loadbalancer-svc.yaml`, `special-services.yaml` |
| 07 | [`07-dns-coredns`](07-dns-coredns) | Service discovery with CoreDNS | 45–46 | commands only |
| 08 | [`08-ingress`](08-ingress) | Ingress controller, path rules, rewrite-target, host rules | 50–54 | `webapp-ingress.yaml`, `webapp-ingress-host.yaml` |
| 09 | [`09-gateway-api`](09-gateway-api) | Gateway API (optional, looking ahead) | 55, 76–77 | `gateway-api.yaml` |
| 10 | [`10-your-app-and-database`](10-your-app-and-database) | Build and load your own image, MySQL on a PVC | 56–60 | `api/Dockerfile`, `webapp-config.yaml`, `webapp-api.yaml`, `mysql-pvc.yaml`, `mysql-deployment.yaml`, `mysql-svc.yaml` |
| 11 | [`11-troubleshooting`](11-troubleshooting) | Debugging storage and networking, "which object do I need?" | 61, 63 | commands only |

## How the folders depend on each other

Each chapter reuses objects from the chapters before it, as the deck does:

```
00 namespace webapp-stack
01 data-pv / data-pvc  ──► 02 seed-content Job, stamp CronJob (mount data-pvc)
03 webapp-config (nginx on 8081), mysql-secret
05 webapp-api Deployment (uses webapp-config)
06 webapp-api-svc :80 → 8081  ──► 07 DNS tests ──► 08 Ingress /home → webapp-api-svc
10 your own webapp-api:v1 image + mysql (mysql-secret, mysql-pvc, mysql-svc)
```

## Quick start

```bash
minikube start --cni=calico
```

```bash
kubectl apply -f 00-setup/namespace.yaml
```

Then go through each folder's `commands.txt` from top to bottom.

## Clean up

```bash
kubectl delete namespace webapp-stack
```

```bash
kubectl delete pv data-pv
```

## Notes

- `storageclass-gp3-aws.yaml`, `encryption-config.yaml` and `09-gateway-api` are **reference only**. They need AWS, a self-managed control plane or extra controllers, so they won't run on plain minikube.
- `mysql-secret.yaml` holds a demo password. Never commit real Secrets.
- `10-your-app-and-database/api` is a tiny nginx image that serves a page on port 8081, so the build → load → run demo works without the Task 2 app.
- In the demo, `mysql-pvc` uses the default `standard` StorageClass. In **Task 2**, students write a static hostPath PV `mysql-pv` themselves.
