# UIT-GO GitOps Kubernetes Manifests

This repository contains the Kubernetes manifests for deploying the UIT-GO microservices platform using GitOps principles with namespace separation. It is designed for use with ArgoCD.

## Structure

The manifests are organized by functional namespaces for better isolation and management:

- `core-services/` - Core microservices (auth-service, driver-service, trip-service)
- `databases/` - Database services (PostgreSQL, MongoDB, Redis, RedisInsight) 
- `messaging/` - Message queue services (Kafka, Zookeeper)
- `monitoring/` - Observability stack (Elasticsearch, Kibana, Logstash)
- `argo-apps/` - ArgoCD Application manifests for each namespace

## Namespace Architecture

### Core Services (`core-services`)
- **auth-service**: Authentication and session management
- **driver-service**: Driver management and location tracking  
- **trip-service**: Trip booking and management

### Databases (`databases`)
- **postgres**: Primary database for auth-service
- **mongodb**: Document database for driver and trip services
- **redis**: Cache and session storage
- **redisinsight**: Redis management UI

### Messaging (`messaging`)
- **kafka**: Event streaming platform
- **zookeeper**: Kafka coordination service

### Monitoring (`monitoring`)
- **elasticsearch**: Search and analytics engine
- **kibana**: Data visualization dashboard
- **logstash**: Log processing pipeline

## Cross-Namespace Communication

Services communicate across namespaces using fully qualified domain names (FQDN):
- `<service>.<namespace>.svc.cluster.local`

Examples:
- Core services connect to databases: `postgres.databases.svc.cluster.local`
- Services connect to messaging: `kafka.messaging.svc.cluster.local`

## Deployment

### Method 1: Deploy All Namespaces
```bash
kubectl apply -f argo-apps/
```

### Method 2: Deploy Individual Namespaces
```bash
# Deploy databases first (dependencies)
kubectl apply -f argo-apps/databases-app.yaml

# Deploy messaging
kubectl apply -f argo-apps/messaging-app.yaml

# Deploy core services
kubectl apply -f argo-apps/core-services-app.yaml

# Deploy monitoring
kubectl apply -f argo-apps/monitoring-app.yaml
```

## Benefits of Namespace Separation

1. **Security Isolation**: Services in different namespaces are isolated by default
2. **Resource Management**: Apply quotas and limits per namespace
3. **Independent Scaling**: Scale and manage each functional group separately  
4. **Faster Operations**: Sync, delete, and rollback operations are faster per namespace
5. **Team Ownership**: Different teams can own different namespaces
6. **Reduced Blast Radius**: Issues in one namespace don't affect others

## Secrets

Each namespace contains its own secrets. Update the base64 encoded values before production:

```bash
# Example: Update postgres password
echo -n "your-new-password" | base64
```

Then update the secret in the respective namespace manifest.

## ArgoCD Applications

Each namespace has its own ArgoCD Application for independent management:
- `uit-go-core-services`: Manages core microservices
- `uit-go-databases`: Manages database infrastructure  
- `uit-go-messaging`: Manages Kafka messaging
- `uit-go-monitoring`: Manages ELK stack

## Quick Start

1. **Install ArgoCD** (if not already installed):
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

2. **Deploy UIT-GO Applications**:
   ```bash
   kubectl apply -f argo-apps/
   ```

3. **Access ArgoCD UI**:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

4. **Get ArgoCD Admin Password**:
   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | ForEach-Object { [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }
   ```

## Monitoring and Troubleshooting

- **Check namespace status**: `kubectl get namespaces`
- **View pods per namespace**: `kubectl get pods -n <namespace>`
- **Check ArgoCD sync status**: Access ArgoCD UI or `kubectl get applications -n argocd`
- **View service connectivity**: `kubectl get svc -A`

---

Generated: 2025-11-27
