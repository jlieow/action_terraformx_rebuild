# Sample workflow to reference to this action

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
          aws_profiles: |
            [
              {
                "aws_access_key_id": ${{ toJSON(secrets.AWS_HUB_PROFILE_AWS_ACCESS_KEY_ID) }},
                "aws_secret_access_key": ${{ toJSON(secrets.AWS_HUB_PROFILE_AWS_SECRET_ACCESS_KEY) }},
                "profile_name": ${{ toJSON(secrets.AWS_HUB_PROFILE_NAME) }}
              }
            ]
          az_service_principals: |
            [
              {
                "az_client_id": ${{ toJSON(secrets.AZ_USERNAME) }},
                "az_client_secret": ${{ toJSON(secrets.AZ_PASSWORD) }},
                "az_tenant_id": ${{ toJSON(secrets.AZ_TENANT_ID) }},
                "az_subscription_id": ${{ toJSON(secrets.AZ_SUBSCRIPTION_ID) }}
              }
            ]
          gcp_profiles: |
            [
              {
                "sa1_profile_name": ${{ secrets.SA1_KEY }}
              }
            ]
```

Read [GCP Info](GCP_INFO.MD) for GCP specific instructions