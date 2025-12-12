# Demo2 Redis with Web Interface - Quick Start Guide

## 🎯 What's Included

This Helm chart deploys:

1. **Redis 7.2** - High-performance in-memory database
2. **Web Interface** - Beautiful Node.js web app for managing Redis
3. **Persistent Storage** - 1Gi PVC for data persistence
4. **Health Monitoring** - Liveness and readiness probes

## 🚀 Quick Deploy

### Deploy to Kubernetes

```bash
# Using Helm
helm install my-redis ./demo2 --namespace team3-workloads --create-namespace

# Check deployment status
kubectl get all -n team3-workloads
```

### Access the Web Interface

```bash
# Port forward the webapp
kubectl port-forward svc/my-redis-webapp 3000:3000 -n team3-workloads

# Open in browser
http://localhost:3000
```

## 🌐 Web Interface Features

The web interface provides a beautiful, modern UI for Redis management:

### **Set Key-Value Pairs**
- Enter a key and value
- Click "Set Value" to store in Redis
- Instant feedback in the output panel

### **Get Values**
- Enter a key name
- Retrieve the stored value
- See null for non-existent keys

### **List All Keys**
- Click "List All Keys"
- View all keys currently in Redis
- JSON-formatted output

### **Delete Keys**
- Enter key name
- Confirmation dialog before deletion
- Safe deletion with feedback

### **Redis Server Info**
- Click "Redis Info"
- View detailed server statistics
- Memory usage, uptime, and more

### **Real-time Status**
- Connection status indicator
- Updates every 5 seconds
- Green = Connected, Red = Disconnected

## 📊 Architecture

```
┌─────────────────────────────────────────┐
│         Kubernetes Cluster              │
│                                         │
│  ┌──────────────┐    ┌──────────────┐  │
│  │   Web App    │───▶│    Redis     │  │
│  │  (Node.js)   │    │   (7.2)      │  │
│  │  Port: 3000  │    │  Port: 6379  │  │
│  └──────────────┘    └──────────────┘  │
│         │                    │          │
│         │                    │          │
│    ┌────▼────┐         ┌────▼────┐     │
│    │ Service │         │   PVC   │     │
│    │ (webapp)│         │  (1Gi)  │     │
│    └─────────┘         └─────────┘     │
└─────────────────────────────────────────┘
         │
         │ Port Forward
         ▼
    Your Browser
   (localhost:3000)
```

## 🔧 Configuration

### Enable/Disable Web Interface

Edit `values.yaml`:

```yaml
webapp:
  enabled: true  # Set to false to disable
```

### Adjust Resources

```yaml
webapp:
  resources:
    limits:
      cpu: 100m
      memory: 128Mi
    requests:
      cpu: 50m
      memory: 64Mi
```

## 🧪 Testing

### Test Redis Connection

```bash
# Run Helm tests
helm test my-redis -n team3-workloads

# Manual test with redis-cli
kubectl run redis-test --rm -it --restart=Never \
  --image redis:7.2-alpine -n team3-workloads \
  -- redis-cli -h my-redis PING
```

### Test Web Interface

1. Port forward: `kubectl port-forward svc/my-redis-webapp 3000:3000 -n team3-workloads`
2. Open browser: `http://localhost:3000`
3. Try setting a key: `mykey` = `Hello World`
4. Retrieve it: Get key `mykey`
5. List all keys
6. Delete the key

## 📝 Example Usage

### Via Web Interface

1. **Set a key:**
   - Key: `user:1:name`
   - Value: `John Doe`
   - Click "Set Value"

2. **Get the key:**
   - Key: `user:1:name`
   - Click "Get Value"
   - See: `"John Doe"`

3. **List all keys:**
   - Click "List All Keys"
   - See: `["user:1:name"]`

### Via Redis CLI

```bash
kubectl port-forward svc/my-redis 6379:6379 -n team3-workloads

# In another terminal
redis-cli -h localhost -p 6379

# Commands
SET session:abc123 "user_data"
GET session:abc123
DEL session:abc123
KEYS *
```

## 🎨 Web Interface Screenshots

The web interface features:
- **Purple gradient header** with Redis logo
- **Card-based layout** for different operations
- **Real-time status indicator** (Connected/Disconnected)
- **Dark terminal-style output** with green text
- **Smooth animations** on button hover
- **Responsive design** for mobile and desktop

## 🔒 Security

- Redis runs as **non-root user** (UID 999)
- Web app runs with **minimal privileges**
- **No external access** by default (ClusterIP)
- **ConfigMap-based** configuration (no secrets in code)

## 📦 Components

| Component | Image | Port | Purpose |
|-----------|-------|------|---------|
| Redis | `redis:7.2-alpine` | 6379 | Data storage |
| Web App | `node:20-alpine` | 3000 | Web interface |

## 🚨 Troubleshooting

### Web app not starting?

```bash
# Check webapp logs
kubectl logs -l app.kubernetes.io/component=webapp -n team3-workloads

# Check if Redis is running
kubectl get pods -n team3-workloads
```

### Can't connect to Redis?

```bash
# Verify Redis service
kubectl get svc -n team3-workloads

# Test Redis connectivity
kubectl run redis-test --rm -it --restart=Never \
  --image redis:7.2-alpine -n team3-workloads \
  -- redis-cli -h my-redis PING
```

### Port forward not working?

```bash
# Make sure the service exists
kubectl get svc my-redis-webapp -n team3-workloads

# Check if port 3000 is already in use
netstat -ano | findstr :3000
```

## 📚 Additional Resources

- [Redis Documentation](https://redis.io/docs/)
- [Express.js Guide](https://expressjs.com/)
- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/)
- [Helm Charts](https://helm.sh/docs/topics/charts/)

## 🎓 Learning Objectives

This demo teaches:
- ✅ Multi-container Helm deployments
- ✅ ConfigMap-based application code
- ✅ Init containers for dependency installation
- ✅ Service-to-service communication in Kubernetes
- ✅ Port forwarding for local access
- ✅ Health checks and probes
- ✅ Resource management

## 🤝 Contributing

This is a demo project. Feel free to:
- Add more Redis commands to the web interface
- Improve the UI design
- Add authentication
- Implement Redis Pub/Sub features
- Add data visualization

---

**Enjoy your Redis Web Interface! 🎉**
