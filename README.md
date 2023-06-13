# aks-handson

Run on the Cloud shell, tested in the Bash version.

## Connecting 

`az account set --subscription <subscription id>`

`az aks get-credentials --resource-group <aks-rg> --name <aks-name>`

`kubelogin convert-kubeconfig -l azurecli`

`kubectl get deployments`

## Create your own namespace

`kubectl get namespaces`

`kubectl create namespace <your namespace>`

`kubectl config set-context --current --namespace=my-namespace`


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
