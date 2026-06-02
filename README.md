# sample-helm-repo

A Helm-based GitOps repository using the **App of Apps** pattern with ArgoCD.

## Structure

```
sample-helm-repo/
├── apps/                        # Parent chart — bootstrap once
│   ├── Chart.yaml
│   ├── values.yaml              # Global ArgoCD config + per-app toggles
│   └── templates/
│       ├── nginx-application.yaml
│       └── react-application.yaml
└── charts/
    ├── nginx/                   # Nginx app — deployed to the `test` namespace on port 8081
    └── react/                   # React app — placeholder (disabled by default)
```

## How it works

1. The `apps/` chart is bootstrapped **once** via Helm
2. ArgoCD detects the `Application` CRs and deploys each child chart automatically
3. All future changes are driven by Git — push a change, ArgoCD syncs it

## Prerequisites

- Kubernetes cluster
- ArgoCD installed in the cluster
- Helm 3

## Bootstrap

Update `repoURL` in `apps/values.yaml` to point to your Git repo, then run:

```bash
helm install app-of-apps ./apps --namespace argocd --create-namespace
```

## Adding a new app

1. Create a new chart under `charts/my-app/`
2. Add `apps/templates/my-app-application.yaml` with the ArgoCD `Application` CR
3. Add the app under the `apps:` block in `apps/values.yaml` with `enabled: true`
4. `git push` — ArgoCD deploys it automatically, no further commands needed

## Enabling / disabling apps

Edit `apps/values.yaml`:

```yaml
apps:
  nginx:
    enabled: true   # deployed
  react:
    enabled: true   # uncomment and set to true to deploy
```