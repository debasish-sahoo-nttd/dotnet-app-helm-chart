#

## Helm push to ACR

```bash
az acr login --name dsopublic
helm plugin install https://github.com/chartmuseum/helm-push.git
helm package fdc-dotnetcore-app
helm push fdc-dotnetcore-app-0.1.0.tgz oci://dsopublic.azurecr.io/helm
```

## Helm install from ACR

```bash

# Authenticate
USER_NAME="00000000-0000-0000-0000-000000000000"
PASSWORD=$(az acr login --name dsopublic --expose-token --output tsv --query accessToken)

helm registry login dsopublic.azurecr.io \
  --username $USER_NAME \
  --password $PASSWORD

helm install sample-dotnetcore-app oci://dsopublic.azurecr.io/helm/fdc-dotnetcore-app --version 0.1.0 --dry-run

helm upgrade  sample-dotnetcore-app  oci://dsopublic.azurecr.io/helm/fdc-dotnetcore-app --version 0.1.0 -f override_values.yaml

# these doesn't work
helm repo add myrepo oci://dsopublic.azurecr.io/helm
helm repo update
helm install myapp myrepo/fdc-dotnetcore-app --version 0.1.0
```