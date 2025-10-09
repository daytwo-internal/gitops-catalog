# GitOps Catalog

A curated collection of Kustomize bases and overlays for OpenShift operators, inspired by the [Red Hat Community of Practice GitOps Catalog](https://github.com/redhat-cop/gitops-catalog/).

## Overview

This repository provides declarative GitOps-ready configurations for deploying and managing OpenShift operators using Kustomize. Each operator includes a base configuration and versioned overlays for different release channels.

## Included Operators

- **OpenShift GitOps Operator** - GitOps/ArgoCD operator for OpenShift
- **OpenShift Cert Manager Operator** - Certificate management for OpenShift
- **Red Hat Keycloak Operator** - Identity and access management

## Usage

Apply an operator directly using `oc` or `kubectl`:

```bash
# Apply a specific overlay
oc apply -k openshift-gitops-operator/overlays/latest

# Apply the base configuration
oc apply -k openshift-cert-manager-operator/overlays/stable-v1
```

Or reference in your own `kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - github.com/daytwo-internal/gitops-catalog/openshift-gitops-operator/overlays/latest?ref=main
```

## Structure

Each operator follows this structure:
```
operator-name/
├── base/              # Base Kustomize configuration
│   ├── kustomization.yaml
│   ├── namespace.yaml
│   ├── operatorgroup.yaml
│   └── subscription.yaml
├── overlays/          # Version-specific overlays
│   └── version/
│       └── kustomization.yaml
└── README.md          # Operator-specific documentation
```

## Contributing

Feel free to add more operators or improve existing configurations following the established structure.
