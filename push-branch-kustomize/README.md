This example contains a kustomize app that contains annotations to configure
git write-back method and custom write-back branch. 
`deployment.yaml` uses an old version of nginx: `1.16.0`, and upon running
image-updater, the container image tag should be updated to `1.16.1`

## test push-branch-kustomize app
```bash
# to install the push-branch-kustomize Argo CD application as a plain manifest
kubectl apply -f push-branch-kustomize/app/push-branch-kustomize.yaml

# to view the application pod
kubectl get pod -l app=push-branch-kustomize -n argocd

# to view the current container image name and image tag
kubectl get pod -l app=push-branch-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.0

# to check the updated image tag
kubectl get pod -l app=push-branch-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.1

# to delete the app
kubectl delete -f push-branch-kustomize/app/push-branch-kustomize.yaml

```