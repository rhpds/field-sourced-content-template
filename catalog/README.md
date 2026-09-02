# Field Source Content catalog

Stubs only. The Helm chart (or Ansible-via-Helm chart) stays in the author's
repository — or, for the two in-repo examples, under `examples/`.

An ApplicationSet (not in this repo; it ships with the Field Source Content CI)
lists every file in `tested/` as an unsynced Argo Application. `untested/` is
visible in git for review; it is not generated into Argo.

This layout is additive. Existing users who clone this repository and order
the CI with a path of `examples/helm` or `examples/ansible` are unchanged.

## Stub schema

Each file is YAML. Argo CD's ApplicationSet git files generator flattens the
keys into template parameters (`name`, `repoURL`, `path`, `requires.virt`, …).

```yaml
# Argo Application name. DNS-1123: lowercase, digits, hyphens. Max 63 chars.
name: example-helm

# Shown in docs. Not used as the Application name.
displayName: Example (Helm)

description: One or two sentences. What someone gets if they click Sync.

# Git repo that contains the Helm chart. This repo, or someone else's.
repoURL: https://github.com/rhpds/field-sourced-content-template.git
revision: main

# Path to the chart inside repoURL. Use "." for the repository root.
path: examples/helm

# Who to ping when the chart is broken. Not RHDP platform.
owner: Name or GitHub handle

# Optional. Used later to hide or warn when the ordered cluster cannot run this.
requires:
  virt: false
  rhoai: false
  aap: false
  clusterSize: sno   # sno | multinode
```

Required: `name`, `repoURL`, `revision`, `path`.
Use `path: "."` when the chart is at the repository root.

## Adding a stub

1. Copy `catalog/tested/example-helm.yaml`.
2. Change `repoURL` / `path` / `revision` to the author's chart.
3. Open a PR into `catalog/untested/` first; promotion to `tested/` is a
   separate PR once someone has actually synced it.

Do not copy the author's templates into this repository.
