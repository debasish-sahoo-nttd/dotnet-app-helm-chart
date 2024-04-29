#  Helm Chart for Dotnet Core App

This helm chart is used to deploy a dotnet core app to kubernetes.
It also supports pulling the secrets from Azure Key Vault and injecting them into the pod through volume mounts and environment variables.

## Authenticate to ACR

```bash
# Authenticate
USER_NAME="00000000-0000-0000-0000-000000000000"
PASSWORD=$(az acr login --name dsopublic --expose-token --output tsv --query accessToken)

helm registry login dsopublic.azurecr.io \
  --username $USER_NAME \
  --password $PASSWORD
```

## Helm push to ACR

```bash
az acr login --name dsopublic
helm plugin install https://github.com/chartmuseum/helm-push.git
helm package dotnet-app-helm-chart
helm push dotnet-app-0.1.0.tgz oci://dsopublic.azurecr.io/helm
```

## Helm install from ACR

Installing the chart from ACR is not the same as installing from a helm registry. The following commands are not working.

```bash
helm repo add myrepo oci://dsopublic.azurecr.io/helm
helm repo update
helm install myapp myrepo/dotnet-app
```

Instead, you need to follow the below steps to install the chart from ACR.

```bash
# Ensure you are logged in to the ACR
helm install sample-dotnetcore-app oci://dsopublic.azurecr.io/helm/fdc-dotnetcore-app --version 0.1.0 --dry-run

helm upgrade  sample-dotnetcore-app  oci://dsopublic.azurecr.io/helm/fdc-dotnetcore-app --version 0.1.0 -f override_values.yaml

```