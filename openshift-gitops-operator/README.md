# OpenShift GitOps Operator

Installs the OpenShift GitOps Operator.

Do not use the `base` directory directly, as you will need to patch the `channel` based on the version of OpenShift you are using, or the version of the operator you want to use.

The current *overlays* available are for the following channels:

* [latest](overlays/latest)
