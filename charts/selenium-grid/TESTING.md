# Testing Selenium Grid Helm Chart

All related testing to this helm chart will be documented in this file.

## Test Traceability Matrix

| Features               | TC Description                                                       | Coverage | Test via |
|------------------------|----------------------------------------------------------------------|----------|----------|
| Basic Auth             | Basic Auth is disabled                                               | &check;  | Cluster  |
|                        | Basic Auth is enabled                                                | &check;  | Cluster  |
| Auto scaling           | Auto scaling with `enableWithExistingKEDA` is `true`                 | &check;  | Cluster  |
|                        | Auto scaling with `scalingType` is `job`                             | &check;  | Cluster  |
|                        | Auto scaling with `scalingType` is `deployment`                      | &check;  | Cluster  |
|                        | Auto scaling with `autoscaling.scaledOptions.minReplicaCount` is `0` | &check;  | Cluster  |
|                        | Parallel tests execution against node autoscaling                    | &check;  | Cluster  |
| Ingress                | Ingress is enabled without `hostname`                                | &check;  | Cluster  |
|                        | Ingress is enabled with `hostname` is set                            | &check;  | Cluster  |
|                        | Hub `sub-path` is set with Ingress `ImplementationSpecific` paths    | &check;  | Cluster  |
|                        | `ingress.nginx` configs for NGINX ingress controller annotations     | &check;  | Template |
| Distributed components | `isolateComponents` is enabled                                       | &check;  | Cluster  |
|                        | `isolateComponents` is disabled                                      | &check;  | Cluster  |
| Browser Nodes          | Node `nameOverride` is set                                           | &check;  | Cluster  |
|                        | Sanity tests in node                                                 | &check;  | Cluster  |
|                        | Video recorder is enabled in node                                    | &check;  | Cluster  |
|                        | Node `extraEnvironmentVariables` is set value                        | &check;  | Cluster  |
| General                | Set new image registry via `global.seleniumGrid.imageRegistry`       | &check;  | Cluster  |
|                        | Components are able to set `.affinity`                               | &check;  | Template |
| Tracing                | Enable tracing via `SE_ENABLE_TRACING`                               | &check;  | Cluster  |
|                        | Disable tracing via `SE_ENABLE_TRACING`                              | &check;  | Cluster  |
| `Node` component       | `SE_NODE_PORT` can set a port different via `.port`                  | &check;  | Cluster  |
|                        | Extra ports can be exposed on container via `.ports`                 | &check;  | Cluster  |
|                        | Extra ports can be exposed on Service via `.service.ports`           | &check;  | Cluster  |
|                        | Service type change to `NodePort`, specific NodePort can be set      | &check;  | Cluster  |

## CI Test Traceability Matrix

We have a CI pipeline to test the Helm chart with the following test cases.

| Matrix                    | job      | deployment | job_https | job_hostname | deployment_https |
|---------------------------|----------|------------|-----------|--------------|------------------|
| Cluster                   | Kind     | Kind       | Kind      | Minikube     | Minikube         |
| Kubernetes version        | v1.25.16 | v1.26.14   | v1.27.11  | v1.28.7      | v1.29.2          |
| Autoscaling               | x        | x          | x         | x            | x                |
| Scaling type `deployment` |          | x          |           |              | x                |
| Scaling type `job`        | x        |            | x         | x            |                  |
| Full distributed mode     | x        |            |           |              | x                |
| Enable basic auth         |          | x          |           | x            | x                |
| Ingress `hostname`        | x        |            |           | x            | x                |
| Ingress `K8S_PUBLIC_IP`   |          | x          | x         |              |                  |
| HTTPS `tls.enabled`       |          |            | x         |              | x                |
| Enable tracing            | x        | x          |           | x            |                  |
| Enable video recorder     | x        | x          | x         | x            | x                |
| Enable video uploader     | x        | x          | x         | x            | x                |
| Test headless             |          | x          | x         |              |                  |

## Test Chart Template
- By using `helm template` command, the chart template is tested without installing it to Kubernetes cluster.
- Templates are rendered and the output as a YAML manifest file. The manifest file is then asserted with [pyyaml](https://pyyaml.org/wiki/PyYAMLDocumentation).
- Set of values are used to render the templates located in [tests/charts/templates/render](../../tests/charts/templates/render).

```bash
# Back to root directory
cd ../..

# Build chart dependencies and lint
make chart_build

# Test chart template
make chart_test_template
```
- Build chart dependencies and lint requires [Chart Testing `ct`](https://github.com/helm/chart-testing). There is a config file [ct.yaml](../../tests/charts/config/ct.yaml) to configure the chart testing.

## Build & test Docker images with deploy to Kubernetes cluster
Noted: These `make` commands are composed and tested on Linux x86_64.
Run entire commands to build and test Docker images with Helm charts in local environment.

```bash
# Back to root directory
cd ../..

# Setup Kubernetes environment
make setup_dev_env

# Build Docker images
make build

# Build and lint charts
make chart_build

# Setup Kubernetes cluster
make chart_cluster_setup

# Test Selenium Grid on Kubernetes
make chart_test_autoscaling_deployment

make chart_test_autoscaling_job

# Cleanup Kubernetes cluster
make chart_cluster_cleanup
```
- Setup Kubernetes environment requires [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/) and [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
- Set of values are used to deploy the chart to Kubernetes cluster located in [tests/charts/ci](../../tests/charts/ci).
