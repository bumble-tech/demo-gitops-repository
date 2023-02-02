# GitOps repository: Two-level overlays

GitOps repository with a dummy application for demonstration purposes. Related to article: https://medium.com/bumble-tech/gitops-for-multi-cluster-k8s-environments-d305431ba6d6

## Repository structure
```shell
├── base
│   └── hello-app
│       ├── base
│       │   ├── kustomization.yaml
│       │   └── manifest.yaml
│       └── bumble-overlay
│           ├── kustomization.yaml
│           └── patches
│               └── bumble_patch.yaml
└── overlays
    ├── zone-1
    │   ├── argocd-applications
    │   │   └── hello-app.yaml
    │   ├── hello-app
    │   │   ├── kustomization.yaml
    │   │   ├── params.env
    │   │   └── params.yaml
    │   └── kustomization.yaml
    └── zone-2
        ├── argocd-applications
        │   └── hello-app.yaml
        ├── hello-app
        │   ├── kustomization.yaml
        │   ├── params.env
        │   └── params.yaml
        └── kustomization.yaml
```

This repo serves as an example of how to apply two-level overlay structure to a GitOps repo. 

1. `hello-app` is one of our applications. 
2. `base/hello-app/bumble-overlay` contains a company-specific overlay that: 
   1. Updates image of container to `bash:latest`
   2. Replaces `DRAGON_NAME` environment variable with a placeholder that we're filling in zone overlays
3. `overlays/zone-1/hello-app` contains a zone-specific overlay for `hello-app` that replaces the `DRAGON_NAME` placeholder with a name of the dragon specified in `overlays/zone-1/hello-app/params.env`

By following these simple steps, we've introduced two changes to the base resource:
1. company-specific but zone-agnostic change
2. zone-specific change

Leaving the resource itself as is. 

So, with this structure, both company-specific and zone-specific changes are transparent and clear, and next time we need `hello-app` upgraded, we're just replacing `base/manifest.yaml` with the one provided by developer. Same applies to more complex resources.


### NOTE
You may need to disable load restrictor by passing `--load-restrictor none` or `--load-restrictor LoadRestrictionsNone` to kustomize. 
