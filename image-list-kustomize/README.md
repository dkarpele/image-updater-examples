This is a kustomize sample app that configures multiple updateable images via
`argocd-image-updater.argoproj.io/image-list` annotation. All images in that
annotation value should be updated after a successful updater run.

`deployment.yaml` uses image `nginx:latest`, and `deployment2.yaml` uses a
different image `bitnami/nginx:latest`.

When using `digest` update strategy, these image tags will be updated to the 
digest matching the most recent version of the configured mutable tag.

To use `git` write-back-method, add the following annotation to the application yaml:
```yaml
argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/git-creds
```
To use `argocd` write-back-method (the default), remove the above annotation, or set
its value to `argocd`:
```yaml
argocd-image-updater.argoproj.io/write-back-method: argocd
```

## test image-list-kustomize app
```bash
# to create the secret to access the git repo, if using git write-back-method
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install the image-list-kustomize Argo CD application as a plain manifest
kubectl apply -f image-list-kustomize/app/image-list-kustomize.yaml

# to view the application pod
kubectl get pod -l app=image-list-kustomize -n argocd

# to view the current container image name and image tag
kubectl get pod -l app=image-list-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:latest

# to check the updated image tag
kubectl get pod -l app=image-list-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:latest@sha256:0a399eb

# to delete the app
kubectl delete -f image-list-kustomize/app/image-list-kustomize.yaml

```