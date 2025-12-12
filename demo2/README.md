# Demo2 - Redis Helm Chart

A simple Helm chart for deploying Redis with custom configuration on Kubernetes.

## Features

- ✅ **Redis 7.2** (Alpine-based for smaller image size)
- ✅ **Custom Configuration** via ConfigMap
- ✅ **Persistent Storage** with PVC support
- ✅ **Health Probes** (liveness and readiness)
- ✅ **Resource Limits** (CPU and Memory)
- ✅ **Horizontal Pod Autoscaling** (optional)
- ✅ **Security Context** (runs as non-root user)
- ✅ **Service Account** with RBAC support
- ✅ **Ingress** support (optional)

## Installation

### Using Helm CLI

```bash
# Install the chart
helm install my-redis ./demo2

# Install with custom values
helm install my-redis ./demo2 -f custom-values.yaml

# Install in a specific namespace
helm install my-redis ./demo2 --namespace redis-demo --create-namespace
```

### Using ArgoCD

Add this to your ApplicationSet or Application manifest:

```yaml
source:
  repoURL: https://github.com/rmkprabhu/gitops-bridge-workloads-demo
  targetRevision: main
  path: demo2
  helm:
    releaseName: my-redis
    valueFiles:
    - values.yaml
```

## Configuration

### Key Values

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of Redis replicas | `1` |
| `image.repository` | Redis image repository | `redis` |
| `image.tag` | Redis image tag | `7.2-alpine` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Redis service port | `6379` |
| `persistence.enabled` | Enable persistent storage | `true` |
| `persistence.size` | PVC size | `1Gi` |
| `resources.limits.cpu` | CPU limit | `200m` |
| `resources.limits.memory` | Memory limit | `512Mi` |

### Custom Redis Configuration

Edit the `configMap.redisConfig` in `values.yaml`:

```yaml
configMap:
  redisConfig: |
    maxmemory 256mb
    maxmemory-policy allkeys-lru
    save 900 1
```

## Testing

Run Helm tests to verify the deployment:

```bash
helm test my-redis
```

## Accessing Redis

### Port Forward (for local testing)

```bash
kubectl port-forward svc/my-redis 6379:6379
redis-cli -h localhost -p 6379
```

### From within the cluster

```bash
kubectl run redis-client --rm --tty -i --restart='Never' \
  --image redis:7.2-alpine -- redis-cli -h my-redis
```

## Uninstallation

```bash
helm uninstall my-redis
```

## Example Redis Commands

Once connected:

```redis
# Set a key
SET mykey "Hello World"

# Get a key
GET mykey

# Check connection
PING

# Get server info
INFO server
```

## Differences from gitops-bridge-workloads-demo

| Feature | gitops-bridge-workloads-demo | demo2 |
|---------|------------------------------|-------|
| **Application** | Nginx web server | Redis cache |
| **Port** | 80 (HTTP) | 6379 (Redis) |
| **Persistence** | Not enabled | Enabled by default |
| **Health Checks** | HTTP probes | TCP socket probes |
| **Use Case** | Web content serving | Caching/data store |

## License

This is a demo chart for educational purposes.
