# Operational Runbook

## Service Health Monitoring

### Health Check Commands

```bash
# Basic health check
curl https://just-testing.azurelaboratory.com/api/v1/healthz

# Detailed service info
curl https://just-testing.azurelaboratory.com/api/v1/details

# Check pod status
kubectl get pods -l app.kubernetes.io/name=just-testing

# View recent logs
kubectl logs -l app.kubernetes.io/name=just-testing --tail=50
```

## Common Issues & Troubleshooting

### Issue: Service Returning 502/503 Errors

**Symptoms**: Users getting gateway errors, health checks failing

**Investigation Steps:**

1. Check pod status: `kubectl get pods -l app.kubernetes.io/name=just-testing`
2. Examine pod logs: `kubectl logs <pod-name>`
3. Verify service endpoints: `kubectl get endpoints just-testing`

**Common Causes:**

- Pod crash loop (check resources/limits)
- Readiness probe failing
- Service selector mismatch

### Issue: TLS Certificate Problems

**Symptoms**: Browser security warnings, curl SSL errors

**Investigation Steps:**

1. Check certificate status: `kubectl get certificate just-testing-tls`
2. Examine cert-manager logs: `kubectl logs -n cert-manager deployment/cert-manager`
3. Verify DNS resolution: `nslookup just-testing.azurelaboratory.com`

### Issue: Deployment Stuck

**Symptoms**: Argo CD shows "Progressing" status indefinitely

**Investigation Steps:**

1. Check Argo CD application: Visit [Argo CD Dashboard](https://argocd.azurelaboratory.com)
2. Review deployment events: `kubectl describe deployment just-testing`
3. Check image pull status: `kubectl describe pod <pod-name>`

## Scaling Operations

### Manual Scaling

```bash
# Scale to 3 replicas
kubectl scale deployment just-testing --replicas=3

# Or via Helm
helm upgrade just-testing ./charts/just-testing --set replicaCount=3
```

### Monitoring Scaling Metrics

- CPU/Memory usage in Azure Monitor
- Request latency and throughput
- Pod restart frequency

## Emergency Procedures

### Rolling Back Deployment

```bash
# Via Argo CD (recommended)
# Use the Argo CD UI to rollback to previous version

# Via kubectl (emergency only)
kubectl rollout undo deployment/just-testing

# Check rollout status
kubectl rollout status deployment/just-testing
```

### Service

```bash
# Force pod restart
kubectl rollout restart deployment/just-testing
```
