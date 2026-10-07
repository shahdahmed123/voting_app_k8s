# 🗳️ Voting App on Kubernetes

A multi-service voting application deployed on Kubernetes (kubeadm, single node),
exposed through an NGINX Ingress. Users vote between two options, and results
appear in real time on a separate page.

Based on the Docker sample voting app images (`dockersamples/examplevotingapp_*`).
This repo contains only the Kubernetes manifests.

## Architecture

```
Browser → Ingress (vote.local / result.local)
              │
        ┌─────┴──────┐
      vote         result
        │             │
      redis ← worker → postgres (PV/PVC)
```

| Component | Role | Exposure |
|-----------|------|----------|
| vote (Flask) | Voting page, 2 replicas | via Ingress |
| redis | Temporary vote queue | ClusterIP |
| worker (.NET) | Moves votes from Redis to Postgres | none |
| postgres | Persistent results storage | ClusterIP |
| result (Node.js) | Live results page | via Ingress |

## Kubernetes concepts used

- Namespace, Deployments, Services (ClusterIP)
- Secret for database credentials
- PersistentVolume + PersistentVolumeClaim (data survives Pod deletion)
- Ingress with host-based routing (NGINX Ingress Controller)
- Readiness / liveness / startup probes
- Resource requests and limits
- `Recreate` strategy for the database (a single writer on the volume)

## Prerequisites

- A running Kubernetes cluster and `kubectl`
- NGINX Ingress Controller (bare metal):
```bash
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.15.1/deploy/static/provider/baremetal/deploy.yaml
```
- Recommended: 2+ CPUs and 4 GB+ RAM

## Deploy

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/
kubectl get pods -n voting
```

The PersistentVolume uses `hostPath` (`/mnt/data/postgres`), suitable for a
single-node lab cluster only.

## Access

Add this line to your `hosts` file, using your node's IP:

```
<NODE-IP>  vote.local  result.local
```

Then open:
- http://vote.local
- http://result.local

## Notes

- `db-secret.yaml` contains demo credentials (`postgres/postgres`) because the
  sample images expect them. Never commit real secrets to Git.
- With a plain Flannel CNI, NetworkPolicies are not enforced.

## Cleanup

```bash
kubectl delete -f k8s/
```
