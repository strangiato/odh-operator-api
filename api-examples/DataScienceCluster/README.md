# DataScienceCluster

This document is a detailed breakdown of the proposal for the `DataScienceCluster` object and the purpose of each object.

## spec

### spec.components

This is currently implemented as an object similar to the [ArgoCD](https://argocd-operator.readthedocs.io/en/latest/reference/argocd/) Custom Resource.  Alternatively, components could be implemented as a list for each component similiar to the [QuayRegistry](https://access.redhat.com/documentation/en-us/red_hat_quay/3.8/html/deploy_red_hat_quay_on_openshift_with_the_quay_operator/operator-deploy) Custom Resource.

Each component should contain the following objects:

* `enabled` - This flag is the primary control for which components are enabled and which are not. This is the primary way in which cluster admins would manage which specific components are to be installed in this cluster.

Each component should also support the following optional objects.  These objects may auto-populate in the object with default options if the user does not provide them, or they may not auto-populate and instead utilize the default options unless the user provides them:

* `resources` - This object contains standard resource details for k8s pod (request/limits).  It should control the resources for the controller pod for that component.
* `replicas` - This object contains a standard replica details for the k8s pod.  This object should control the number of replicas for this components controller deployment.

#### spec.components.dashboard

This components contains the implementation details for deploying the ODH Dashboard component.

This component contains the following unique configuration options:

* `rbac` - This object contains capabilities to configure permissions for the ODH Dashboard components.  This object should control permissions configured in the dashboard detailed in the `Settings -> Custer settings` section of the RHODS [Side navigation](https://access.redhat.com/documentation/en-us/red_hat_openshift_data_science/1/html-single/getting_started_with_red_hat_openshift_data_science/index#side_navigation)

#### spec.components.notebookController

This component contains the implementation details for deploying the notebook controller component and some of the related notebook resources.

This component contains the following unique configuration options:

* `workbenchImages.managed` - This object enables the creation of default notebook/workbench images that ship with ODH/RHODS.  More fine grained control may be useful for the types of images admins may want to enable or disable.  For example, we may wish to allow the admins to enable things such as enable or disable `python` or `r` images.  The operator should be intelligent in which images it does install on the cluster.  For example, the operator should be able to detect if the NVIDIA GPU operator is available on the cluster and automatically install the CUDA enabled images.
* `notebookNamespace` - This is the namespace that jupyter instances start in when not started as part of a Workbench/Data Science Project.  Not sure what the long term plan is for this capability or if we can do away with it.  I'm not crazy about the idea of a namespace scoped object controlling items in another namespace.

#### spec.components.dataSciencePipelinesController

This component contains the implementation details for deploying the data science pipelines operator controller component.

#### spec.components.grafana

This component contains the implementation details for deploying the grafana component.

#### spec.components.prometheus

This component contains the implementation details for deploying the prometheus component.

### spec.sso

The sso component is responsible for configuring SSO endpoints for all components.  The operator should be aware of which components require routes and configure the necessary SSO for each component.

Initially the Operator would likely only support `openShiftOAuth` for authentication but additional SSO providers could be added in the future if required.
