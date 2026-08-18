# OpenShift Dev Spaces

Installs the OpenShift Dev Spaces Operator (browser-based VS Code
workspaces on the cluster, `che-code` editor) and a `CheCluster` instance
configured with one workspace per user in its own `<username>-devspaces`
namespace.

The Dev Workspace Operator is **not** a separate resource here — OLM
installs it automatically as a declared dependency of the `devspaces`
package, no extra `Subscription` needed.

Do not apply `base` directly: it has two placeholders that must be
patched by an overlay before this works —

- `spec.channel` of the `Subscription` (see `overlays/stable`).
- `spec.devEnvironments.allowedSources.urls` of the `CheCluster` — the
  git URL prefix(es) allowed as a workspace source (e.g.
  `https://github.com/your-org/*`). Left as `PATCH-ME` in `base` and in
  `overlays/stable` on purpose: this is specific to whoever consumes this
  component, there's no sane shared default.

Applying the `Subscription` and the `CheCluster` in the same `oc apply -k`
works, but only once the operator's CRD exists — the first apply after a
fresh install will fail with `no matches for kind "CheCluster"` until the
`Subscription` finishes installing. Retry the apply in a loop instead of
splitting this into two components:

```bash
until oc apply -k openshift-devspaces/overlays/stable; do sleep 30; done
```

## Resource footprint

Each running workspace is a real pod (a few hundred MB to several GB
depending on the image used) — `maxNumberOfRunningWorkspacesPerCluster`
in `base` caps the total at 20 concurrent workspaces; override it in an
overlay if the cluster has different capacity.
