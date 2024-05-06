#  Helm Chart for Dotnet Core App

This helm chart is used to deploy a `dotnet core` app to kubernetes cluster. This chart can be extended to deploy any microservice written in any language as long as it accepts inputs and secrets (pulled from Azure Key vault) in form of environment variables.

It also supports pulling the secrets from Azure Key Vault (by using k8s csi plugin) and injecting them into the pod through volume mounts and environment variables.

## Authenticate to ACR

This form of authentication is using temporary token to authenticate to the ACR. This is useful when you are using helm registry plugin to push the chart to ACR.

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
helm install sample-dotnetcore-app oci://dsopublic.azurecr.io/helm/dotnet-app --version 0.1.0 --dry-run

helm upgrade  sample-dotnetcore-app  oci://dsopublic.azurecr.io/helm/dotnet-app --version 0.1.0 -f override_values.yaml

```