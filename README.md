# App Runtime Deployments - Concourse on GCP Kubernetes

Concourse setup project for the ARD WG Concourse. Main project is https://github.com/cloudfoundry/app-runtime-interfaces-infrastructure.

*Note on worker scaling*: The number of worker nodes is defined in [terragrunt/concourse-wg-ci/config.yaml](./terragrunt/concourse-wg-ci/config.yaml). Autoscaling is not really supported with the current Concourse setup, so set the maximum number of nodes equal to the node count. The number of worker pods is equal to the number of nodes.

If a major stemcell release is coming the [update-stemcell-and-recompile-releases](https://concourse.wg-ard.ci.cloudfoundry.org/teams/main/pipelines/update-releases/jobs/update-stemcell-and-recompile-releases) job will be triggered. This job requires more capacity and the number of nodes should be scaled up to at least 16 and then down to 8 again.

The BOSH director for the "greengrass" environment should also be scaled up to 16 CPUs for the build duration:
https://github.com/cloudfoundry/relint-envs/blob/2b7e0e74f3a99ef209afd5637ead9e11ffb4140d/environments/test/greengrass/bbl-config/scale-up-for-compilation.yml#L13

## Troubleshooting

### CredHub Certificate Expired

The CredHub certificate is currently only valid for 30 days. When it expires, you see orange Concouse jobs/resources with this error message:
```
Get "https://credhub.concourse.svc.cluster.local:9000/api/v1/data?name-like=%2Fconcourse%2Fmain%2Fcf-deployment%2Fard_wg_gitbot_ssh_key": x509: certificate has expired or is not yet valid
```

To recreate the certificates, apply the workaround described in [https://github.com/cloudfoundry/app-runtime-interfaces-infrastructure/issues/61]:
```
gcloud auth login
gcloud config set project cf-deployments-wg
gcloud container clusters get-credentials wg-ci --region europe-west3-a
kubectl cluster-info
kubectl -n concourse get pods # search credhub pod
kubectl -n concourse delete pod credhub-abc-xyz
```
