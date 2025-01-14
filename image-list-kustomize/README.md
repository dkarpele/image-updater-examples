This example contains a kustomize app that contains annotations to configure
multiple images and git write-back method. 
`deployment.yaml` uses an old version of nginx: `1.16.0`, and upon running
image-updater, the container image tag should be updated to `1.16.1`

## test image-list-kustomize app
```bash
# to create the secret to access the git repo
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install the image-list-kustomize Argo CD application as a plain manifest
kubectl apply -f image-list-kustomize/app/image-list-kustomize.yaml

# to view the application pod
kubectl get pod -l app=image-list-kustomize -n argocd

# to view the current container image name and image tag
kubectl get pod -l app=image-list-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.0

# to check the updated image tag
kubectl get pod -l app=image-list-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.1

# to delete the app
kubectl delete -f image-list-kustomize/app/image-list-kustomize.yaml

```