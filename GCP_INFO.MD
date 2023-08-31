# Additional Info For Using GCP

### Deploying through github actions

When deploying on GCP with terraform, the provider requires credentials which needs to be set as either the contents of the key file or the path to the key file. Since it is unsafe to input the key file contents directly, we use the path to the key file instead. We also set the variable for the key path through terraform_env_variables within the workflow file.

provider.tf

```
provider "google" {
  credentials = var.path_to_sa1
}
```

---

The json file name for terraform_env_variables **MUST** be the same as the key name you provide in gcp_profiles.

For example, if your json file name is `sa1_profile_name.json`, the key name needs to be `sa1_profile_name` as shown in the example below.

Otherwise, GCP will not be able to located the correct credential.

workflow.yml

```
terraform_env_variables: |
    TF_VAR_path_to_sa1=${{ github.workspace }}/sa1_profile_name.json
...
gcp_profiles: |
    [
        {
        "sa1_profile_name": ${{ secrets.SA1_KEY }}
        }
    ]
```

### To deploy locally

We can set the variables as an environmental variable for Terraform

```
TF_VAR_path_to_sa1=path_to_key_file_dir/sa1_profile_name.json
```

## Multiple Accounts with GCP

### Deploying through github actions

Take note again that the json file name for terraform_env_variables **MUST** be the same as the key name you provide in gcp_profiles.

For example, if your json file name is `sa1_profile_name.json`, the key name needs to be `sa1_profile_name`. If your json file name is `sa2_profile_name.json`, the key name needs to be `sa2_profile_name` as shown in the example below.

Otherwise, GCP will not be able to located the correct credential.

workflow.yml

```
name: Perform Terraformx Rebuild with Destroy and Apply Action using Linux exe
on:
  push:
    tags:
      - 'dev/rebuild/ec2/v[012].[0-9]+.[0-9]+' # Make sure this matches up with the dir set below
      - 'dev/rebuild/all_terraform_root_in_repo/v[012].[0-9]+.[0-9]+'

jobs:
  provision_resources:
    runs-on: ubuntu-latest
    name: Perform Terraformx Rebuild with Destroy and Apply on Terraform Root EC2
    steps:

      - name: Checkout
        uses: actions/checkout@v3

      - name: Perform Terraformx Rebuild with Destroy and Apply
        id: terraformx_rebuild_with_destroy_apply
        uses: jlieow/action_terraformx_rebuild@V1 # Uses an action in the repo terraformx_apply owned by jlieow tagged as V1
        with:
          dir: ec2 # Remember to set the tag dir too
          terraform_env_variables: |
            TF_VAR_path_to_sa1=${{ github.workspace }}/sa1_profile_name.json
            TF_VAR_path_to_sa2=${{ github.workspace }}/sa2_profile_name.json
          gcp_profiles: |
            [
              {
                "sa1_profile_name": ${{ secrets.SA1_KEY }}
              },
              {
                "sa2_profile_name": ${{ secrets.SA2_KEY }}
              }
            ]
```

---

We define multiple providers with their own credentials.

provider.tf

```
provider "google" {
  alias = "provider1"
  credentials = var.path_to_sa1
}

provider "google" {
  alias = "provider2"
  credentials = var.path_to_sa2
}
```

---

Setting both variables to their own key path in the workflow

workflow.yml

```
terraform_env_variables: |
    TF_VAR_path_to_sa1=${{ github.workspace }}/sa1_profile_name.json
    TF_VAR_path_to_sa2=${{ github.workspace }}/sa2_profile_name.json
```

### To deploy locally

Setting both variables to their own key path through .env for local deployment

config/.env

```
TF_VAR_path_to_sa1=path_to_key_file_dir/sa1_profile_name.json
TF_VAR_path_to_sa2=path_to_key_file_dir/sa2_profile_name.json
```
