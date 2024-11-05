This is a sample Argo CD application using app-of-apps pattern. It contains the following 3 apps:
* `root`: the root app, configured in `root/root.yaml`, that bootstraps the 2 child apps.
* `app1`: child app 1, configured in `apps/app1.yaml`, and sourced from `source/overlays/app1`
* `app2`: child app 2, configured in `apps/app2.yaml`, and sourced from `source/overlays/app2`

`deployment.yaml` uses an old version of nginx: `1.10.0`, and upon running
image-updater, the container image tag should be updated to `1.11.x` for app1,
and to `1.12.x` for app2.

## test push-branch-kustomize app
```bash
# to create the secret to access the git repo
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install root app
kubectl apply -f app-of-apps/root/root.yaml

# to view all apps
kubectl get -n argocd apps

# to delete the root app
kubectl delete -f app-of-apps/root/root.yaml

```