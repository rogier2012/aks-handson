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


## Deploy using Helm

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
