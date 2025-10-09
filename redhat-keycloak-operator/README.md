# Red Hat Build of Keycloak Operator

Installs the Red Hat Build of Keycloak (RHBK) Operator.

Do not use the `base` directory directly, as you will need to patch the `channel` based on the version of OpenShift you are using, or the version of the operator you want to use.

The current *overlays* available are for the following channels:

* [stable-v26](overlays/stable-v26)
