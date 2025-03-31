# Hoarder Helm Chart

This Helm chart deploys the Hoarder application on a Kubernetes cluster, with support for ArgoCD and Traefik ingress.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- ArgoCD (optional, for GitOps deployments)
- Traefik Ingress Controller (for external access)

## Installation

### Standard Helm Installation

```bash
# Install the chart with the release name 'hoarder'
helm install hoarder ./hoarder-helm-chart --namespace hoarder --create-namespace

# Alternatively, use a custom values file
helm install hoarder ./hoarder-helm-chart -f custom-values.yaml --namespace hoarder --create-namespace
```

### ArgoCD Installation

1. Create an Application manifest:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hoarder
  namespace: argocd
spec:
  project: default
  source:
    repoURL: git@github.com:your-org/hoarder-helm-chart.git
    targetRevision: HEAD
    path: .
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: hoarder
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

2. Apply it to your cluster:

```bash
kubectl apply -f hoarder-application.yaml
```

### Traefik IngressRoute Setup

For Traefik integration, create an IngressRoute in the traefik-ingress application:

```yaml
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: hoarder
  namespace: hoarder
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`hoarder.yourdomain.com`)
      kind: Rule
      services:
        - name: web
          port: 3000
  tls:
    certResolver: letsencrypt
```

## Configuration

The following table lists the configurable parameters of the Hoarder chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount.web` | Number of Web replicas | `1` |
| `replicaCount.chrome` | Number of Chrome replicas | `1` |
| `replicaCount.meilisearch` | Number of Meilisearch replicas | `1` |
| `image.web.repository` | Web image repository | `ghcr.io/hoarder-app/hoarder` |
| `image.web.tag` | Web image tag | `latest` |
| `image.web.pullPolicy` | Web image pull policy | `Always` |
| `image.chrome.repository` | Chrome image repository | `gcr.io/zenika-hub/alpine-chrome` |
| `image.chrome.tag` | Chrome image tag | `123` |
| `image.chrome.pullPolicy` | Chrome image pull policy | `IfNotPresent` |
| `image.meilisearch.repository` | Meilisearch image repository | `getmeili/meilisearch` |
| `image.meilisearch.tag` | Meilisearch image tag | `v1.11.1` |
| `image.meilisearch.pullPolicy` | Meilisearch image pull policy | `IfNotPresent` |
| `persistence.data.enabled` | Enable persistence for data | `true` |
| `persistence.data.size` | Size of PVC for data | `1Gi` |
| `persistence.data.storageClass` | Storage class for data PVC | `""` |
| `persistence.data.accessMode` | Access mode for data PVC | `ReadWriteOnce` |
| `persistence.meilisearch.enabled` | Enable persistence for Meilisearch | `true` |
| `persistence.meilisearch.size` | Size of PVC for Meilisearch | `1Gi` |
| `persistence.meilisearch.storageClass` | Storage class for Meilisearch PVC | `""` |
| `persistence.meilisearch.accessMode` | Access mode for Meilisearch PVC | `ReadWriteOnce` |
| `ingress.enabled` | Enable built-in ingress | `false` |
| `ingress.className` | Ingress class name | `""` |
| `ingress.host` | Ingress host | `hoarder.synik4l.net` |
| `ingress.annotations` | Ingress annotations | `{}` |
| `ingress.tls` | Ingress TLS configuration | `[]` |
| `env.NEXTAUTH_URL` | NextAuth URL | `http://localhost:3000` |
| `env.HOARDER_VERSION` | Hoarder version | `release` |

## Secrets

For proper operation, you need to provide the following secrets:

```yaml
secrets:
  NEXTAUTH_SECRET: "your-nextauth-secret"
  MEILI_MASTER_KEY: "your-meilisearch-master-key"
  NEXT_PUBLIC_SECRET: "your-next-public-secret"
  # Optionally
  # OPENAI_API_KEY: "your-openai-api-key"
```

You can generate secure random strings using:

```bash
openssl rand -base64 36
``` 