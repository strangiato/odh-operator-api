# ODH Operator API Proposal

This repo is intended to provide possible design details for a future ODH Operator.

This operator should have a set of different custom resources that are originated from, owned, and managed by this operator.  There may also be additional custom resources that are originated from other operators that this operator deploys that should be exposed to the end user through this operator in the `Installed Operators` page, such as the `ModelMesh` custom resources.

## Standards

All custom resources should not deploy manifests from an arbitrary git repo and all manifests should be constructed from objects contained in the operator code.

All custom resources owned and managed by the operator should report details of the state of the operator using the `status` section of the object.

The `status` section should include a `conditions` list that contains details on the state of the object.  These conditions should be able to be mapped back to an ArgoCD health state of `Progressing`, `Degraded`, or `Healthy`.

When reporting an issue in the custom resource, the `status` section should report useful information that helps to detail what the issue is causing the degraded state and which components the issue is related to.

All objects deployed by an instance of the custom resource should be able to report and track the `Resources` that are managed and deployed by that custom resources.  These resources should be viable through the UI using the `Resources` tab of the `Installed Operators` custom resource page.

Any resources installed by a custom resource should be able to be uninstalled by removing that custom resource.

## ODH Operator Owned Custom Resources

### DataScienceCluster

The `DataScienceCluster` is designed to create an opinionated install of odh components.  

This resource could be automatically configured on install similar to the default ArgoCD instance that is created by OpenShift-GitOps but should be managed by the user (or Argo) after the initial install.  The operator should reconcile changes to this object after it's initial creation, and report the status on the object.

The primary user of this object is likely a cluster-administrator with minimal knowledge of data science processes, workflows, or tools.  The configuration should provide easy configuration with a reasonable initial configuration.  This initial configuration should error on the side of providing "all" of the features, and allow users to selectively "turn off" the pieces that they don't need.

#### Motivations

This resource should enable users to configure the components they need on their cluster.  Not all clusters will be "development" clusters that need things like ODH-Dashboard/CodeFlare/DataSciencePipelines and may only need things like ModelMesh/TrustyAI/Prometheus/Grafana for serving in a production environment.

SSO configuration should be managed in a single location and the operator will intelligently configure things like the OAuth Proxy Sidecar on each pod that is exposed via a route.  For now, OpenShift OAuth is likely the only supported SSO provider but in the future additional SSO providers may be required by customers.

Future components should be able to be added to the object without breaking backwards compatibility but components should be first class citizens of the operator.  The operator should be aware of the types of objects each component should deploy as well as how different components deployments may change when multiple components are deployed.  For example, deploying both the notebookController and the odhDashboard may also deploy several OdhQuickStart objects to enable the notebookController components in the odhDashboard UI.  If the odhDashbaord is deployed without the notebookController, those objects would not be deployed.

The operator should also be able to intelligently respond to details about the cluster.  For example, the operator should be able to detect when the NVIDIA GPU Operator is available and automatically make changes to the configuration to enable GPU capabilities.  For example, if the GPU Operator is installed, the operator should be able to make CUDA enabled images available automatically.

## Third Party Custom Resources Exposed Through ODH Operator

This are additional operators that the ODH Operator may deploy as components and the corresponding Custom Resources those objects manage.  Some of these objects may be exposed to the end user through the ODH Operator page in the Installed Operators UI in OpenShift.

### Data Science Pipeline 

- DataSciencePipelinesApplication

### Model Mesh

- Model Mesh Server
- Model Mesh Models
