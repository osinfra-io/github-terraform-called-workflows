# <img align="left" width="45" height="45" src="https://user-images.githubusercontent.com/1610100/201473670-e0e6bdeb-742f-4be1-a47a-3506309620a3.png"> Terraform Google Cloud Platform Called Workflows

**[GitHub Actions](https://github.com/osinfra-io/github-terraform-gcp-called-workflows/actions):**

[![Dependabot](https://github.com/osinfra-io/github-terraform-gcp-called-workflows/actions/workflows/local-dependabot.yml/badge.svg)](https://github.com/osinfra-io/github-terraform-gcp-called-workflows/actions/workflows/local-dependabot.yml)

**[Bridgecrew](https://www.bridgecrew.cloud/projects?types=Passed&repository=osinfra-io%2Fgithub-terraform-gcp-called-workflows&branch=main):**

[![Infrastructure Tests](https://www.bridgecrew.cloud/badges/github/osinfra-io/github-terraform-gcp-called-workflows/general)](https://www.bridgecrew.cloud/link/badge?vcs=github&fullRepo=osinfra-io%2Fgithub-terraform-gcp-called-workflows&benchmark=INFRASTRUCTURE+SECURITY)

Reusing workflows avoids duplication. This makes workflows easier to maintain and allows you to create new workflows
more quickly by building on the work of others, just as you do with actions.

Workflow reuse also promotes best practice by helping you to use workflows that are well designed, have already been
tested, and have been proved to be effective. Your organization can build up a library of reusable workflows that can
be centrally maintained.

## Reusing Workflows

Rather than copying and pasting from one workflow to another, you can make workflows [reusable](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows). You and anyone with access to the reusable workflow can then call the reusable workflow from another workflow.

### Workflows

- [gcp-plan-and-apply.yml](.github/workflows/gcp-plan-and-apply.yml)
- [infracost.yml](.github/workflows/infracost.yml)

### Example Google Cloud Platform Usage

```yaml
name: Development

on:
  push:
    branches:
      - main

jobs:
  global_infra:
    name: "Global"
    uses: osinfra-io/github-terraform-called-workflows/.github/workflows/gcp-plan-and-apply-called.yml@v0.0.0
    with:
      checkout_ref: ${{ github.ref }}
      github_env: "Development Infrastructure: Global"
      service_account: nonprod-serviceaccount@iam.gserviceaccount.com
      terraform_version: 1.3.4
      tf_plan_args: -var-file=tfvars/dev.tfvars
      tf_state_bucket: nonprod-state-bucket
      tf_workspace: nonprod-workspace
      working_dir: global
      workload_identity_provider: projects/PROJECT_NUMBER/locations/global/workloadIdentityPools/github-actions/providers/github-actions-oidc
    secrets:
      gpg_passphrase: ${{ secrets.GPG_PASSPHRASE }}
      ssh_key: ${{ secrets.SSH_PRIV_KEY }}
      tf_plan_secret_args: -var="token=${{ secrets.TOKEN }}"

  us_east1_infra:
    name: "Infra: us-east1"
    uses: osinfra-io/github-terraform-called-workflows/.github/workflows/gcp-plan-and-apply-called.yml@v0.0.0
    needs: global_infra
    with:
      checkout_ref: ${{ github.ref }}
      github_env: "Development Infrastructure: Regional - us-east1"
      service_account: nonprod-serviceaccount@iam.gserviceaccount.com
      terraform_version: 1.3.4
      tf_plan_args: -var-file=tfvars/us-east1-dev.tfvars
      tf_state_bucket: nonprod-state-bucket
      tf_workspace: nonprod-workspace-us-east1
      working_dir: regional
      workload_identity_provider: projects/PROJECT_NUMBER/locations/global/workloadIdentityPools/github-actions/providers/github-actions-oidc
    secrets:
      gpg_passphrase: ${{ secrets.GPG_PASSPHRASE }}
      ssh_key: ${{ secrets.SSH_PRIV_KEY }}

   us_east4_infra:
    name: "Infra: us-east4"
    uses: osinfra-io/github-terraform-called-workflows/.github/workflows/gcp-plan-and-apply-called.yml@v0.0.0
    needs: global_infra
    with:
      checkout_ref: ${{ github.ref }}
      github_env: "Development Infrastructure: Regional - us-east4"
      service_account: nonprod-serviceaccount@iam.gserviceaccount.com
      terraform_version: 1.3.4
      tf_plan_args: -var-file=tfvars/us-east4-dev.tfvars
      tf_state_bucket: nonprod-state-bucket
      tf_workspace: nonprod-workspace-us-east4
      working_dir: regional
      workload_identity_provider: projects/PROJECT_NUMBER/locations/global/workloadIdentityPools/github-actions/providers/github-actions-oidc
    secrets:
      gpg_passphrase: ${{ secrets.GPG_PASSPHRASE }}
      ssh_key: ${{ secrets.SSH_PRIV_KEY }}
```

### Example Infracost Usage

```yaml
name: Infracost

on:
  pull_request:

jobs:
  infracost:
    name: Infracost
    uses: osinfra-io/github-terraform-called-workflows/.github/workflows/infracost.yml@v0.0.0
    secrets:
      git_token: ${{ secrets.GITHUB_TOKEN }}
      infracost_api_key: ${{ secrets.INFRACOST_API_KEY }}
      ssh_key: ${{ secrets.SSH_PRIV_KEY }}
```
