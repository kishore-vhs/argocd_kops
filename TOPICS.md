### Deploying ArgoCD and Deploying application
 
```
kubectl create namespace argocd
```
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
```
VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
```
```
sudo curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64
```
```
sudo chmod +x /usr/local/bin/argocd
```
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
```
export ARGOCD_SERVER=`kubectl get svc argocd-server -n argocd -o json | jq --raw-output .status.loadBalancer.ingress[0].hostname`
```
### For ArgoCD Password
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
``` 
argocd login $ARGOCD_SERVER --username admin --password <Password> --insecure
```
```
kubectl config unset contexts.default
```
```
CONTEXT_NAME=$(kubectl config view -o jsonpath='{.contexts[].name}')
```
```
argocd cluster add $CONTEXT_NAME
```