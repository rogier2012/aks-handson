# aks-handson



Run on the Cloud shell, tested in the Bash version.

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

Define a name for your image e.g. `rogier-voting`

`git clone https://github.com/Azure-Samples/azure-voting-app-redis.git`

`cd azure-voting-app-redis/azure-vote/`


` az acr build --image rogier-voting:v1 --registry bngworkshopacr --file Dockerfile .`

After this command, find your image in https://portal.azure.com/#@bngbank.onmicrosoft.com/resource/subscriptions/1771ed3d-7e62-4569-a0ea-bbb0880b4d0b/resourcegroups/Workshop-Cloudchapter-rg/providers/Microsoft.ContainerRegistry/registries/bngworkshopacr/repository

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
- Change image.repository to `<container-registry-name>.azurecr.io/rogier-voting`
- Change image.tag to v1
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

This reveals the external IP from which you can check out 
  
For more exercises, check out:
https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/
gcr.io/google-samples/kubernetes-bootcamp:v1

https://www.getbetterdevops.io/helm-quickstart-tutorial/


## Exploring the cluster

https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/

`kubectl get pods`

`kubectl describe pods`

### Proxy
`kubectl proxy`

`export POD_NAME="$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')"
echo Name of the Pod: $POD_NAME`


`curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/`

### Logs

`kubectl exec "$POD_NAME" -- env`

`kubectl exec -ti $POD_NAME -- bash`

`cat server.js`

`curl http://localhost:8080`

## Scaling
https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/

`kubectl get deployments`


`kubectl get rs`

`kubectl scale deployments/kubernetes-bootcamp --replicas=4`

`kubectl get deployments`


`kubectl get pods -o wide`

`kubectl describe deployments/kubernetes-bootcamp`
