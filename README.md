# Nautilus

A generic reusable Helm chart for deploying any web application. It provides standard Kubernetes resources (Deployment, Service, Ingress, ConfigMap) that any team can use without writing their own templates.

## What it creates

| Resource | Always created | Condition |
|----------|---------------|-----------|
| Deployment | yes | — |
| Service | yes | — |
| Ingress | no | `ingress.enabled: true` |
| ConfigMap | no | `configMap.enabled: true` |

---

## How to use Nautilus in your app

### Step 1 — Reference Nautilus as a dependency in your `Chart.yaml`

```yaml
apiVersion: v2
name: my-app
version: 0.1.0
dependencies:
  - name: nautilus
    version: "0.1.0"
    repository: "http://chartmuseum.chartmuseum.svc.cluster.local:8080"
    alias: app
```

> The `alias: app` means all Nautilus values are namespaced under `app:` in your `values.yaml`.

### Step 2 — Override values in your `values.yaml`

```yaml
app:
  app:
    name: my-app
  image:
    repository: nginx
    tag: "1.27"
  replicaCount: 2
  containerPort: 80
  service:
    type: ClusterIP
    port: 80
  ingress:
    enabled: true
    className: traefik
    host: my-app.local
    path: /
    pathType: Prefix
  resources:
    requests:
      cpu: 50m
      memory: 64Mi
    limits:
      cpu: 100m
      memory: 128Mi
```

### Step 3 — Pull the dependency and install

```bash
helm dependency update .
helm install my-app .
```

---

## Values reference

| Key | Required | Description |
|-----|----------|-------------|
| `app.name` | yes | Name used for all Kubernetes resources |
| `image.repository` | yes | Container image repository |
| `image.tag` | yes | Container image tag |
| `image.pullPolicy` | no | Default: `IfNotPresent` |
| `replicaCount` | no | Default: `1` |
| `containerPort` | no | Port the container listens on. Default: `80` |
| `service.type` | no | `ClusterIP`, `NodePort`, `LoadBalancer`. Default: `ClusterIP` |
| `service.port` | no | Default: `80` |
| `ingress.enabled` | no | Default: `false` |
| `ingress.className` | no | Default: `traefik` |
| `ingress.host` | no | Hostname for the ingress rule |
| `configMap.enabled` | no | Default: `false` |
| `configMap.data` | no | Key-value pairs injected as env vars |
| `env` | no | List of `name/value` env vars |
| `resources` | no | CPU/memory requests and limits |

---

## Passing environment variables

```yaml
app:
  env:
    - name: DB_HOST
      value: "postgres:5432"
    - name: APP_ENV
      value: "production"
```

## Passing config via ConfigMap

```yaml
app:
  configMap:
    enabled: true
    data:
      DB_HOST: postgres:5432
      APP_ENV: production
```
