#Argo Workflow

### Install and Patch

```
# Install
kubectl create namespace argo
kubectl apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.4.8/install.yaml

# turn off tls and server login
kubectl patch -n argo deploy argo-server --patch-file argo-server-patch.yaml

# open https://localhost:8080
kubectl port-forward svc/argocd-server -n argo 9090:2746

```

### Other config

- Follow below guide to create config.json file
[kaniko docker hub config](https://github.com/GoogleContainerTools/kaniko#pushing-to-docker-hub)

```
# create docker login secret
kubectl create secret generic registry-credential \
    --from-file=.dockerconfigjson=<path/to/config.json> \
    --type=kubernetes.io/dockerconfigjson

# create github pat secret
kubectl apply -f github-secret.yaml

```