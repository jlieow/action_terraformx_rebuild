Sample workflow to reference to this action

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
        uses: jlieow/terraformx_rebuild@V1 # Uses an action in the repo terraformx_apply owned by jlieow tagged as V1
        with:
          dir: ec2 # Remember to set the tag dir too
          aws_profiles: |
            [
              {
                "aws_access_key_id": ${{ toJSON(secrets.AWS_HUB_PROFILE_AWS_ACCESS_KEY_ID) }},
                "aws_secret_access_key": ${{ toJSON(secrets.AWS_HUB_PROFILE_AWS_SECRET_ACCESS_KEY) }},
                "profile_name": ${{ toJSON(secrets.AWS_HUB_PROFILE_NAME) }}
              },
              {
                "aws_access_key_id": ${{ toJSON(secrets.AWS_SPOKE_PROFILE_AWS_ACCESS_KEY_ID) }},
                "aws_secret_access_key": ${{ toJSON(secrets.AWS_SPOKE_PROFILE_AWS_SECRET_ACCESS_KEY) }},
                "profile_name": ${{ toJSON(secrets.AWS_SPOKE_PROFILE_NAME) }}
              }
            ]
```
