# chart-fluentbit-log 

Deploys [Fluent Bit](https://fluentbit.io/).  Tailored to collect security events from [NeuVector](https://neuvector.com/) and forward to an Azure Log Analytics workspace.

This deployment with expose two service:
- **fluentbit-log** running on port **5140** - fluentbit will listen on this port and expect calls from nuevector
- **fluentbit-log-web** running on port **2020** - fluentbit will expose API on this port whcih can be used from oher pod running inside the cluster

## Example configuration

```yaml
namespace: neuvector
image:
  registry: docker.io
  repository: fluent/fluent-bit
  tag: 1.1.2
  imagePullPolicy: IfNotPresent
resources:
  requests:
    memory: "64Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: 1
replicaCount: 1
azure_log_workspace_id: <WORKSPACE_ID>
azure_log_workspace_shared_key: <WORKSPACE_SHARED_KEY>
enableSecret: false
enableAzurePlugin: true
enableStandardOutput: false
logLevel: info
```
If you want to deploy using helm rather then flux make sure to set `enableSecret` to `true` and provide values for `azure_log_workspace_id` and `azure_log_workspace_shared_key`

## Azure DevOps Builds

Builds are run against the 'nonprod' AKS cluster.

### Pull Request Validation

A build is triggered when pull requests are created. This build will run `helm lint`, deploy the chart using `ci-values.yaml` and run `helm test`.

### Release Build

Triggered when the repository is tagged (e.g. when a release is created). Also performs linting and testing, and will publish the chart to ACR on success.
