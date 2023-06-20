# IAF Actions
This repository contains reusable GitHub Actions and Workflows. Inspired by (and copied from) [farfetched-Actions](https://github.com/equinor/farfetched-actions). As a part of moving all pipelines for IAF applications from Azure Devops to Github Actions, we also create and add reusable actions where its possible. 

## Workflows

### Fusion Deploy
This workflow deploys the created artifact(s) in a workflow to the given Fusion Portal environment. It deploys "on behalf of" a Azure ServicePrincipal with Federated Credentials.
The workflow will login into Azure using the given ServicePrincipal, obtain a Token to use against the given resource and then upload the artifact(s) to the Portal using the token.

_Parameters:_
* **fusion-portal-url** (mandatory)
URL to the Portal where the artifact should be deployed.
* **app-key** (mandatory)
The AppKey (name of the package), as used within the Fusion Portal.
* **artifact-path** (mandatory)
The "local" path (with filename) to the artifact to be uploaded to Fusion.
* **publish** (optional)
Set if the artifact should immediately be published in the Portal or not. Default true.
* **azure-tenant-id**
The Azure Tenant ID of the ServicePrincipal (Application registration) which is setup with the proper Federated Credentials.
* **azure-client-id**
The Client ID of the ServicePrincipal.
* **azure-resource-id**
The Resource ID where the artifact should be deployed to. Note that this doesn't have anything to do with resource groups, but the app registration that hosts the desired environment (CI/QA or PROD). For CI/QA it is most likely `5a842df8-3238-415d-b168-9f16a6a6031b` and for PROD it is most likely `97978493-9777-4d48-b38a-67b0b9cd88d2`.

Example (`deploy` here is a _job_ in a GitHub workflow)
```
    deploy:
        name: "🚀 Deploy to Fusion Portal"
        runs-on: ubuntu-latest
        needs: build
        steps:
            - name: "Download artifact"
              uses: actions/download-artifact@v2
              with:
                  name: theNameOfTheArtifactThatWasUploaded.zip

            - name: Deploy to Fusion Portal
              uses: equinor/farfetched-actions/fusion-deploy@v1.0
              with:
                  fusion-portal-url: ${{ secrets.FUSION_PORTAL_URL }}
                  app-key: ${{ env.FUSION_APP_KEY }}
                  artifact-path: theNameOfTheArtifactThatWasUploaded.zip
                  azure-tenant-id: ${{ secrets.AZURE_TENANT_ID }}
                  azure-client-id: ${{ secrets.AZURE_CLIENT_ID }}
                  azure-resource-id: ${{ secrets.AZURE_RESOURCE_ID }}
```