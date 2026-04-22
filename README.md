# cdtktn-actions

Reusable workflows to use GitHub actions to manage CDK Terrain
infrastructure using CI/CD and GCP.

## Setup

Using the workflows requires some configuration of GCP and the GitHub repository containing the CDK code.
These can be automatically provisioned using [cdktn-constructs](https://github.com/curioswitch/cdktn-constructs).

Manual configuration will require for...

GCP:

For each project, for example `dev` and `prod`,

- A workload identity pool and provider for logging in from a GitHub action. The default value is a pool named `github` with a provider named `github`.
- A service account for running Terraform plan - should have at least the `Viewer` IAM role though certain plans require additional
  permissions. The default value is a service account named `terraform-viewer`.
- An IAM membership to the `principal` corresponding to the viewer GitHub environment for the project, such as `dev-viewer` or `prod-viewer`
- A service account for running Terraform apply - should have `Owner` IAM role. The default value is a service account named `terraform-admin`.
- An IAM membership to the `principal` corresponding to the applyer GitHub environment for the project, such as `dev` or `prod`

The cdktf-constructs code can be referenced for how to set up these resources, for example the IAM membership for the `prod`
environment of a repo is configured [here](https://github.com/curioswitch/cdktf-constructs/blob/main/src/gcp-project/index.ts#L187).

GitHub:

- Define a GitHub environment for applying and planning each project you will use, generally `dev-viewer`, `dev`, `prod-viewer`, `prod`. These must
  correspond to the `principal` definitions in the IAM memberships above.
- It is recommended to set protection rules on the environments.
  - `dev-viewer`: none, used for pull request reviews
  - `dev`: `main` branch
  - `prod-viewer`: `release` branches
  - `prod`: `release` branches with reviewers set to sysadmins

With the above configuration, workflows in your repository referencing the reusable workflows here should function. See examples
[here](https://github.com/curioswitch/tasuke-infra/tree/main/.github/workflows).
