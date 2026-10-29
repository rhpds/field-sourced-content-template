# Ansible Example

Deploy workloads using Ansible playbooks executed via a Kubernetes Job. Use this when you need capabilities beyond Helm templating: generating secrets, waiting for resources, conditional logic, or calling external APIs.

## Quick Start

1. Copy this folder to your own repository
2. Edit `values.yaml` - update the repository URL and playbook settings
3. Edit `playbooks/site.yml` - customize the deployment tasks
4. Push to your Git repository
5. Order the **Field Content CI** from RHDP with your repository URL

## Architecture

```
ansible/
├── Chart.yaml
├── values.yaml           # Configuration for Job, collections, extraVars
├── templates/            # Job, RBAC, ConfigMap templates
└── playbooks/            # Your Ansible content
    ├── site.yml          # Main playbook with workflow
    └── roles/            # Reusable roles (operator-install, etc.)
```

ArgoCD deploys this as a Helm chart. The chart creates a Kubernetes Job that clones the playbooks and runs Ansible.

## What Gets Deployed

| Component | Description |
|-----------|-------------|
| Web Terminal | Operator installed via OLM with wait-for-ready |
| SSH Keypair | Generated and stored as Secret (demonstrates Ansible capability) |
| Hello World | Sample httpd application |

## Configuration

All settings are in `values.yaml`. Key sections:

```yaml
ansible:
  repository:
    url: "https://github.com/your-org/your-repo"
    path: "examples/ansible/playbooks"
  playbook: "site.yml"
  extraVars:
    operator_name: "web-terminal"
```

See comments in [values.yaml](values.yaml) and [playbooks/site.yml](playbooks/site.yml) for detailed documentation.

## Testing Locally

```bash
cd playbooks
ansible-galaxy collection install -r requirements.yml
ansible-playbook site.yml \
  -e "cluster_domain=$(oc get ingresses.config.openshift.io cluster -o jsonpath='{.spec.domain}')" \
  -e "namespace=test"
```

## Troubleshooting

```bash
oc get jobs -n ansible-runner
oc logs -n ansible-runner job/ansible-demo
```
