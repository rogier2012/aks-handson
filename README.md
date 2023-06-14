# aks-handson



Run on the Cloud shell, tested in the Bash version. Read the instruction carefully. anything within `<>` you have to fill in yourself.

## Connecting 

`az account set --subscription <subscription id>`

`az aks get-credentials --resource-group <aks-rg> --name <aks-name>`

`kubelogin convert-kubeconfig -l azurecli`

Verify that it works by the following command (or any other kubectl command for that matter)
`kubectl get deployments`

## View the available nodes

`kubectl get nodes`

**How much memory does the `agentpool` node have?**

You can view the properties of a Kubernetes object using the following command

`kubectl describe <object-type> <object-name>`

## Create your own namespace
We want to deploy something without bothering other cluster users. Therefore we are going to create a namespace. Use the following command to see the current namespaces available

`kubectl get namespaces`

Create a new namespace:

`kubectl create namespace <your-namespace>`

Set the default namespace in your context to avoid typing `--namespace <your-namespace>` everytime.

`kubectl config set-context --current --namespace=<your-namespace>`

## Register container in ACR

Define a name for your image e.g. `rogier-voting` and choose a version number e.g. `v1`

`git clone https://github.com/Azure-Samples/azure-voting-app-redis.git`

`cd azure-voting-app-redis/azure-vote/`

Now build and register the app in the Azure Container Registry(ACR). You can find the container registry in the same resource group as the kubernetes cluster

` az acr build --image <image-name>:<version-number> --registry <registry-name> --file Dockerfile .`

After this command, find your image in the Repository section of the ACR

## Creating a Helm project
We are now going to use Helm to deploy something in our namespace. We are following the tutorial from Azure: https://learn.microsoft.com/en-us/azure/aks/quickstart-helm?tabs=azure-cli

Start by creating a new helm project

`helm create <your-project-name>`

Update `<your-project-name>/Chart.yaml` to add a dependency for the redis chart from the https://charts.bitnami.com/bitnami chart repository and update appVersion to v1. Use the built-in code editor to update the `Chart.yaml`
  
`code <your-project-name>/Chart.yaml `
  
Update the dependencies of your Helm project using
  
`helm dependency update <your-project-name>`
  
Update `<your-project-name>/values.yaml` with the following changes:
- Add a redis section to set the image details, container port, and deployment name
- Add a backendName for connecting the frontend portion to the redis deployment
- Change image.repository to `<container-registry-name>.azurecr.io/<image-name>`
- Change image.tag to <version-number> from above
- Change service.type to LoadBalancer
 
Add an env section to `<your-project-name>/templates/deployment.yaml` for passing the name of the redis deployment.

```
containers:
        ...
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
          - name: REDIS
            value: {{ .Values.backendName }}
        ...
```

## Deploy the helm project

`helm install <your-project-name> <your-project-name>/`

It takes a few minutes for the service to return a public IP address. Monitor progress using the `kubectl get service command` with the --watch argument.

This reveals the external IP from which you can check out the app.

## Updating the app

Now we want to personalize the app by editing the button text (or more if you like).

Update the config file:
  
`cd azure-voting-app-redis/azure-vote`
`code azure-vote/config_file.cfg`
  
 Change the vote1value and vote2value to your liking and save.
  

  
 Update the app by creating a new version :
 
 ` az acr build --image <image-name>:<new-version-number> --registry <registry-name> --file Dockerfile .`
  
 Update your helm by changing the following:
  - Update the `appVersion` to a higher number in the `Chart.yaml`
  - Update the tag of image to your new version number in `values.yaml`
  
  
  
## Scaling
Now we are going to manually scale our front-end via `kubectl`. Note that you can also do this by editing the `replicaCount` in the `values.yaml`

  Follow the steps below:
  
`kubectl get deployments`

`kubectl get rs`

`kubectl scale deployments/<your-project-name> --replicas=4`

`kubectl get deployments`

`kubectl get pods -o wide`

`kubectl describe deployments/<your-project-name>`

## Clean up
  
Always cleanup after yourselves. To do this, execute the following command 
  
`helm delete <your-project-name>`
  
  
## More tutorials 
  
  
 
For more exercises, check out:
https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/
gcr.io/google-samples/kubernetes-bootcamp:v1

https://www.getbetterdevops.io/helm-quickstart-tutorial/





