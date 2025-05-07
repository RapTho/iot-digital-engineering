# Deploying mosquitto on IBM Cloud

In this guide, you'll learn how to:

- Push the previously built container image to **IBM Container Registry (ICR)**.
- Deploy the container to **IBM Code Engine**, a fully managed, serverless platform for running containers.

This workflow is ideal for hosting custom services like a secured MQTT broker, built and maintained locally but deployed in the cloud.

## Prerequisites

- mosquitto container image built (previous step)
- IBM Cloud CLI installed
- Logged in with `ibmcloud login --sso`

## Install required plugins

Install the Code Engine and Container Registry plugins:

```
ibmcloud plugin install code-engine container-registry
```

## Set environment variables

These variables make the rest of the commands easier to reuse. Choose a **unique** `IMAGE_NAME`

```
export RESOURCE_GROUP=iot-digital-engineering
export CR_NAMESPACE=hslu-iot-digital-engineering
export IMAGE_NAME=mosquitto-custom
export IMAGE_TAG=1.0
```

> Note: These variables are only available in the current session.

### Setting environment variables on Windows (PowerShell)

If you're using **Windows with PowerShell**, use the following syntax instead of `export`:

```powershell
$env:RESOURCE_GROUP = "iot-digital-engineering"
$env:CR_NAMESPACE = "hslu-iot-digital-engineering"
$env:IMAGE_NAME = "mosquitto-custom"
$env:IMAGE_TAG = "1.0"
$env:API_KEY = "myGeneratedAPIKey"
```

You can access them the same way in subsequent commands (e.g., `$env:IMAGE_NAME`) or just use them inline like `${env:IMAGE_NAME}` if needed in PowerShell scripting.

## Select IBM Cloud context

Select the correct resource group:

```
ibmcloud target -g ${RESOURCE_GROUP}
```

Select your Code Engine project:

```
ibmcloud ce project select --name myProjectName
```

`OPTIONAL`: You can create a new project using

```
ibmcloud ce project create --name myProjectName
```

## Publish image to IBM Container Registry

Set region and log in:

```bash
ibmcloud cr region-set eu-central
ibmcloud cr login --client podman # or --client docker
```

Add namespace (skip if it already exists):

```
ibmcloud cr namespace-add ${CR_NAMESPACE}
```

A **manifest** groups multiple platform-specific container images under one tag, allowing cross-architecture support (e.g., `amd64`, `arm64`). We re-tag the image with the IBM Container Registry URL so it can be correctly identified and pushed to the registry.

```
podman tag ${IMAGE_NAME}:${IMAGE_TAG} de.icr.io/${CR_NAMESPACE}/${IMAGE_NAME}:${IMAGE_TAG}
podman manifest push --all de.icr.io/${CR_NAMESPACE}/${IMAGE_NAME}:${IMAGE_TAG}
```

(Optional) Set image retention policy to keep only the last 2 versions:

```
ibmcloud cr retention-policy-set --images 2 ${CR_NAMESPACE}
```

## Create API key and registry access for Code Engine

Create an IBM Cloud API key:

> **Save the generated API key**, as it's only visible during creation time!

```
ibmcloud iam api-key-create mosquitto-deploy-key-${USER} -d "API Key to deploy mosquitto on IBM Code Engine"
```

Copy and export the API key:

```
export API_KEY="myGeneratedAPIKey"
```

Create a registry access secret for Code Engine:

```
ibmcloud ce registry create --name ibm-container-registry-${USER} --server de.icr.io --username iamapikey --password ${API_KEY}
```

## Authenticate `kubectl` for Code Engine

Follow IBM's guide to enable Kubernetes CLI access to Code Engine: <br />
[https://cloud.ibm.com/docs/codeengine?topic=codeengine-kubernetes](https://cloud.ibm.com/docs/codeengine?topic=codeengine-kubernetes)

## Upload mosquitto configuration files

Use config maps and secrets to upload your local files:

```
ibmcloud ce configmap create --name conf-${USER} --from-file mosquitto.conf=mosquitto/mosquitto.conf
ibmcloud ce configmap create --name acl-${USER} --from-file acl.txt=mosquitto/acl.txt
ibmcloud ce secret create --name passwords-${USER} --from-file passwords.txt=mosquitto/passwords.txt
```

## Deploy mosquitto to Code Engine

Run the following to create and deploy the application:

```
ibmcloud ce app create --name mosquitto-${USER} \
  --image de.icr.io/${CR_NAMESPACE}/${IMAGE_NAME}:${IMAGE_TAG} \
  --registry-secret ibm-container-registry-${USER} \
  --mount-secret /home/mosquitto/passwords=passwords-${USER} \
  --mount-configmap /home/mosquitto/config=conf-${USER} \
  --mount-configmap /home/mosquitto/acl=acl-${USER} \
  --port 8083 \
  --min-scale 1 \
  --max-scale 1 \
  --cpu 0.25 \
  --memory 0.5G
```

### Running on Windows (PowerShell)

In PowerShell, line continuations use backticks (\`) instead of backslashes. Here's the equivalent command:

```powershell
ibmcloud ce app create --name mosquitto-$env:USERNAME `
  --image de.icr.io/$env:CR_NAMESPACE/$env:IMAGE_NAME:$env:IMAGE_TAG `
  --registry-secret ibm-container-registry-$env:USERNAME `
  --mount-secret /home/mosquitto/passwords=passwords-$env:USERNAME `
  --mount-configmap /home/mosquitto/config=conf-$env:USERNAME `
  --mount-configmap /home/mosquitto/acl=acl-$env:USERNAME `
  --port 8083 `
  --min-scale 1 `
  --max-scale 1 `
  --cpu 0.25 `
  --memory 0.5G
```

This will start your mosquitto MQTT broker on IBM Code Engine with your custom configuration, ACLs, and password protection.

## Troubleshooting

### Connect to IBM Code Engine's Kubernetes-API

To use `kubectl` with IBM Code Engine, you can set the context as documented [here](https://cloud.ibm.com/docs/codeengine?topic=codeengine-kubernetes)

```bash
ibmcloud ce project select -n iot-digital-engineering --kubecfg
```

Now you can do everything you're authorized to :)

### Get pods

```bash
kubectl get pods
```

### Check logs of a specific pod

Replace `myPodName` with the name of your pod. You can also use the `-f` option to subscribe to incoming logs

```bash
kubectl logs pod/myPodName
```

If a pod contains more than one container, as it is the case when deploying apps on IBM Code Engine, you can select the container you're interested in using the `-c` option. By default IBM Code Engine calls the user's container `user-container`

```bash
kubectl logs -f pod/myPodName -c user-container
```
