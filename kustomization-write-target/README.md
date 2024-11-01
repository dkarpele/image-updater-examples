This example contains a kustomize app that configures git write-back method and 
`write-back-target: kustomization` 
`deployment.yaml` uses an old version of nginx: `1.16.0`, and upon running
image-updater, the container image tag should be updated to `1.16.1`

## test kustomization-write-target app
```bash
# to create the secret to access the git repo
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install the kustomization-write-target Argo CD application as a plain manifest
kubectl apply -f kustomization-write-target/app/kustomization-write-target.yaml

# to view the application pod
kubectl get pod -l app=kustomization-write-target -n argocd

# to view the current container image name and image tag
kubectl get pod -l app=kustomization-write-target -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.0

# to check the updated image tag
kubectl get pod -l app=kustomization-write-target -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.1

# to delete the app
kubectl delete -f kustomization-write-target/app/kustomization-write-target.yaml
```