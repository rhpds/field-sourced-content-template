# Helm Example - App of Apps

Deploy workloads using an ArgoCD App of Apps pattern. The master chart generates ArgoCD Applications for each enabled component.

## Quick Start

1. Copy this folder to your own repository
2. Edit `values.yaml` - enable/disable components under `components:`
3. Push to your Git repository
4. Order the **Field Content CI** from RHDP with your repository URL

## Architecture

```
helm/
├── Chart.yaml
├── values.yaml              # Central configuration for all components
├── templates/
│   └── applications.yaml    # Generates ArgoCD Applications per component
└── components/
    ├── operator/            # OLM operator subscription
    └── hello-world/         # Sample demo application
```

Each component under `components/` is a standalone Helm chart. The master chart creates ArgoCD Applications that deploy these child charts.

## Components

| Component | Description | Default |
|-----------|-------------|---------|
| `operator` | Install operators via OLM | disabled |
| `helloWorld` | Sample httpd application | enabled |

## Configuration

All settings are in `values.yaml`. Override via Helm values:

```bash
# Enable operator installation
helm template . --set components.operator.enabled=true
```

See comments in [values.yaml](values.yaml) and [templates/applications.yaml](templates/applications.yaml) for detailed documentation.

## Testing Locally

```bash
helm lint .
helm template my-release . --set deployer.domain=apps.cluster.example.com
```

## Adding Components

1. Create a new chart in `components/your-component/`
2. Add configuration under `components.yourComponent` in `values.yaml`
3. Add an Application block in `templates/applications.yaml`
