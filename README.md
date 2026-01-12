# Collection of reusable GitHub workflows

## Run script

Example use case:
- run bash script from Ubuntu with custom environment variables set with single github variable or github secret containing json string with variable names and values
- good when you have multiple environments (prod, dev, ...)
- good if you like having a single github variable containing all variables in json string
- good when you want to use GaaS (github actions as a service) to run scripts so you can easily migrate to any other "script running service" like GitLab or AzureDevOps

Example:

- set github action variable `SCRIPT_ENV_VARS_PROD`:

    ```
    { "USER": "johndoe",
      "ENVIRONMENT": "prod" }
    ```

    and these will be visible to the script like regular environment variables.

- set github action secret `SCRIPT_ENV_VARS_SENSITIVE`:

    ```
    { "PASSWORD": "12345",
      "SOME_TOKEN": "secret" }
    ```

    and these will be visible to the script like regular environment variables but since they are in a secret, they can not be printed.

- Example workflow which uses reusable workflow:

  ```yml
  name: Run script to build PRODUCTION infrastructure

  on:
    push:
      branches:
        - main
    workflow_dispatch: # Allows manual triggering

  jobs:
    create_resources:
      uses: baxpick/workflows/.github/workflows/reusable_run_script.yml@main
      
      with:
        script: "./awesome_script.sh -e prod"
        scriptEnvVars: ${{ vars.SCRIPT_ENV_VARS_PROD }}

      secrets:
        SCRIPT_ENV_VARS_SENSITIVE: ${{ secrets.SCRIPT_ENV_VARS_SENSITIVE_PROD }}
  ```