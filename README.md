# App Runtime Deployments - Concourse on GCP Kubernetes

Concourse setup project for the ARD WG Concourse. Main project is https://github.com/cloudfoundry/app-runtime-interfaces-infrastructure.

*Note on worker scaling*: The number of worker nodes is defined in [terragrunt/concourse-wg-ci/config.yaml](./terragrunt/concourse-wg-ci/config.yaml). Autoscaling is not really supported with the current Concourse setup, so set the maximum number of nodes equal to the node count. The number of worker pods is equal to the number of nodes.

If a major stemcell release is coming the [update-stemcell-and-recompile-releases](https://concourse.wg-ard.ci.cloudfoundry.org/teams/main/pipelines/update-releases/jobs/update-stemcell-and-recompile-releases) job will be triggered. This job requires more capacity and the number of nodes should be scaled up to at least 16 and then down to 8 again.
