# Hoarder Helm Chart

This Helm chart deploys the Hoarder application on a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+

## Installation

```bash
# Add the repository (if applicable)
# helm repo add hoarder https://your-repo-url.com
# helm repo update

# Install the chart with the release name 'hoarder'
helm install hoarder ./hoarder-helm-chart --namespace hoarder --create-namespace

# Alternatively, use a custom values file
helm install hoarder ./hoarder-helm-chart -f custom-values.yaml --namespace hoarder --create-namespace
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
| `image.chrome.repository` | Chrome image repository | `gcr.io/zenika-hub/alpine-chrome` |
| `image.chrome.tag` | Chrome image tag | `123` |
| `image.meilisearch.repository` | Meilisearch image repository | `getmeili/meilisearch` |
| `image.meilisearch.tag` | Meilisearch image tag | `v1.11.1` |
| `persistence.data.enabled` | Enable persistence for data | `true` |
| `persistence.data.size` | Size of PVC for data | `1Gi` |
| `persistence.meilisearch.enabled` | Enable persistence for Meilisearch | `true` |
| `persistence.meilisearch.size` | Size of PVC for Meilisearch | `1Gi` |
| `ingress.enabled` | Enable ingress | `false` |
| `ingress.host` | Ingress host | `hoarder.synik4l.net` |

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