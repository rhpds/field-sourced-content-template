# Field Content

Self-service platform for developing RHDP Catalog Items using GitOps patterns.

## Overview

Create demos and labs for Red Hat Demo Platform without deep AgnosticD knowledge:

1. Clone this template repository
2. Choose an example (`helm/` or `ansible/`) as your starting point
3. Customize the deployment for your use case
4. Push to your Git repository
5. Order the **Field Content CI** from RHDP with your repository URL

ArgoCD deploys your content, and the platform handles health monitoring and data flow back to AgnosticD.

## Getting Started

### Choose Your Pattern

| Pattern | Use When |
|---------|----------|
| [examples/helm/](examples/helm/) | Deployment can be expressed as Kubernetes manifests with Helm templating |
| [examples/ansible/](examples/ansible/) | You need wait-for-ready, secret generation, API calls, or conditional logic |

### Quick Start

```bash
# Clone this template
git clone https://github.com/rhpds/field-sourced-content-template.git my-content
cd my-content

# Choose an example and start customizing
cd examples/helm      # or examples/ansible
# Edit values.yaml and templates as documented in each example's README
```

## How It Works

```
Your Git Repo                    OpenShift Cluster
┌─────────────┐                 ┌─────────────────────────────┐
│ Helm Chart  │──── ArgoCD ────▶│ Your Workload               │
│ (templates, │                 │ (operators, apps)           │
│  values)    │                 └─────────────────────────────┘
└─────────────┘                           │
                                          ▼
                                ConfigMap with demo.redhat.com/userinfo
                                          │
                                          ▼
                                    AgnosticD picks up user info
```

## RHDP Integration

Label resources for platform integration:

```yaml
# Health monitoring
metadata:
  labels:
    demo.redhat.com/application: "my-demo"

# Pass data back to AgnosticD (URLs, credentials, etc.)
metadata:
  labels:
    demo.redhat.com/userinfo: ""
```

## Showroom

The catalog item can deploy a Showroom lab guide for each user from your repo.

- Put `site.yml`, `ui-config.yml`, and `content/` (with `antora.yml`) at the root of the repo. This repo has a working example.
- Check **Deploy Showroom?** when you order.
- Embedded OpenShift console tabs do not work. The terminal tab is set in `ui-config.yml`.
- A bad layout does not fail the order. A clone error or a missing `site.yml` or `content/` gives a warning in the order details. AsciiDoc or `antora.yml` errors give no warning; check the `antora-builder` container logs.
- For a private repo, use a read-only token that is valid until the environment is retired. Showroom users can read it, and it is used again when the pod restarts.

Build the guide locally:

```bash
podman run --rm -v "$PWD:/antora:z" -w /antora --entrypoint antora quay.io/rhpds/antora:v1.3.0 site.yml
```

## Community catalog

`catalog/tested/` holds **stubs**, not copies of charts. Each file points at a
Helm chart: either `examples/` in this repository, or someone else's GitHub.

The two files already there (`example-helm`, `example-ansible`) are the
in-repo examples, stubbed the same way an external demo will be. Change
`repoURL` / `path` and you have the third item.

These stubs do not appear in Argo until the Field Source Content CI grows an
ApplicationSet that reads `catalog/tested/*.yaml`. Until then they are the
schema. Clone-and-order with your own URL still works exactly as above.

See [catalog/README.md](catalog/README.md).

## Documentation

- [examples/helm/README.md](examples/helm/README.md) - Helm deployment guide
- [examples/ansible/README.md](examples/ansible/README.md) - Ansible deployment guide
- [catalog/README.md](catalog/README.md) - Stub schema for the community catalog
- [docs/ansible-developer-guide.md](docs/ansible-developer-guide.md) - In-depth Ansible patterns

## Repository Structure

```
field-sourced-content-template/
├── site.yml         # Showroom example (with ui-config.yml and content/)
├── content/
├── examples/
│   ├── helm/        # Helm starter chart (also the target of the helm stub)
│   └── ansible/     # Ansible starter chart (also the target of the ansible stub)
├── catalog/
│   ├── tested/      # Stubs the ApplicationSet will list in Argo
│   └── untested/    # Stubs not yet promoted
├── roles/
│   └── ocp4_workload_field_content/  # AgnosticD workload role (lives in core_workloads)
└── docs/
```
