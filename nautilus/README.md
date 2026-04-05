# Nautilus Helm Chart

A generic reusable Helm chart for deploying any web application without writing Kubernetes templates.

## Helm Repo

```bash
helm repo add nautilus https://rama3319.github.io/Nautilus
helm repo update
```

## Resources

| Resource | Created | Condition |
|----------|---------|-----------|
| Deployment | always | |
| Service | always | |
| Ingress | optional | `ingress.enabled: true` |
| ConfigMap | optional | `configMap.enabled: true` |

---

## Usage

### 1. Add as a dependency in `Chart.yaml`

```yaml
apiVersion: v2
name: my-app
version: 0.1.0
dependencies:
  - name: nautilus
    version: "0.4.0"
    repository: "https://rama3319.github.io/Nautilus"
    alias: app
```

> `alias: app` namespaces all Nautilus values under `app:` in your `values.yaml`

### 2. Configure in `values.yaml`

```yaml
app:
  app:
    name: my-app              # required
  image:
    repository: nginx         # required
    tag: "1.27"               # required
  replicaCount: 1
  containerPort: 80
  service:
    type: ClusterIP
    port: 80
  ingress:
    enabled: true
    className: traefik
    host: my-app.local
  resources:
    requests:
      cpu: 50m
      memory: 64Mi
    limits:
      cpu: 100m
      memory: 128Mi
```

### 3. Install

```bash
helm dependency update .
helm install my-app .
```

---

## Values Reference

| Key | Required | Default | Description |
|-----|----------|---------|-------------|
| `app.name` | yes | | Name for all Kubernetes resources |
| `image.repository` | yes | | Container image |
| `image.tag` | yes | | Image tag |
| `image.pullPolicy` | no | `IfNotPresent` | |
| `replicaCount` | no | `1` | Number of replicas |
| `containerPort` | no | `80` | Port the container listens on |
| `service.type` | no | `ClusterIP` | `ClusterIP`, `NodePort`, `LoadBalancer` |
| `service.port` | no | `80` | |
| `ingress.enabled` | no | `false` | |
| `ingress.className` | no | `traefik` | |
| `ingress.host` | no | | Hostname for ingress rule |
| `configMap.enabled` | no | `false` | |
| `configMap.data` | no | | Key-value pairs injected as env vars |
| `env` | no | `[]` | List of `name/value` env vars |

---

## Environment Variables

```yaml
app:
  env:
    - name: DB_HOST
      value: "postgres:5432"
```

## ConfigMap

```yaml
app:
  configMap:
    enabled: true
    data:
      DB_HOST: postgres:5432
      APP_ENV: production
```

---

## Releasing a New Version

1. Bump `version` in `Chart.yaml`
2. Push to `main`
3. GitHub Actions packages and publishes the release automatically
