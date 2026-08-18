# HTPasswd Identity Provider

Registers a single HTPasswd identity provider on the cluster-wide `OAuth`
object (`config.openshift.io/v1`, `metadata.name: cluster`).

**This replaces `spec.identityProviders` entirely** — `OAuth` is a
singleton and this is a plain declarative `apply`, not a merge patch. Only
use this component on a cluster where HTPasswd is meant to be the sole
identity provider (a fresh disposable cluster, a lab environment). If the
cluster already has other identity providers you need to keep, patch the
existing `OAuth` object by hand instead of applying this.

Do not apply `base` directly — it has two `PATCH-ME` placeholders (the IDP
`name` and the referenced `Secret` name). There's no sane shared default
for either (they're naming conventions specific to whoever consumes this),
so unlike other components in this catalog there's no `overlays/` here:
write that overlay in your own consuming repo, referencing this `base`
remotely, e.g.:

```yaml
# your-repo/operators/htpasswd-idp/kustomization.yaml
resources:
  - github.com/daytwo-internal/gitops-catalog/htpasswd-identity-provider/base?ref=main

patches:
  - target:
      kind: OAuth
      name: cluster
    patch: |-
      - op: replace
        path: /spec/identityProviders/0/name
        value: your-idp-name
      - op: replace
        path: /spec/identityProviders/0/htpasswd/fileData/name
        value: your-secret-name
```

## What this does NOT do

The `Secret` holding the actual htpasswd file (real usernames/password
hashes) is intentionally **not** part of this component — it's per-cluster
generated data, never something to bake into a shared public catalog.
Create it yourself before or after applying this component (order doesn't
matter — the IDP just shows a degraded state until the secret exists):

```bash
oc create secret generic your-secret-name \
  --namespace openshift-config \
  --from-file=htpasswd=./users.htpasswd \
  --dry-run=client -o yaml | oc apply -f -
```
