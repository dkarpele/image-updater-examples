This is a sample Argo CD application using app-of-apps pattern. It contains the following 3 apps:
* `root`: the root app, configured in `root/root.yaml`, that bootstraps the 2 child apps.
* `app1`: child app 1, configured in `apps/app1.yaml`, and sourced from `source/overlays/app1`
* `app2`: child app 2, configured in `apps/app2.yaml`, and sourced from `source/overlays/app2`

`deployment.yaml` uses an old version of nginx: `1.10.0`, and upon running
image-updater, the container image tag should be updated to `1.11.x` for app1,
and to `1.12.x` for app2.

This sample can be run with write-back-method `git` with the following annotations
on `app1.yaml` and `app2.yaml`:
```yaml
    argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/git-creds
    argocd-image-updater.argoproj.io/write-back-target: kustomization
```
Or run with the default `argocd` write-back-target by removing the above 2 annotations,
or by setting write-back-target to `argocd`:
```yaml
    argocd-image-updater.argoproj.io/write-back-method: argocd
```

## test app-of-apps
```bash
# to create the secret to access the git repo, required if using git write-back-method
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install root app and child apps
kubectl apply -f app-of-apps/root/root.yaml

# to view all apps
kubectl get -n argocd apps

NAME   SYNC STATUS   HEALTH STATUS
app1   Synced        Healthy
app2   Synced        Healthy
root   Synced        Healthy

# to run image-updater from the command line
argocd-image-updater/dist/argocd-image-updater run
... ...
INFO[0005] git push origin main                          dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-app2940891903 execID=d97d5
INFO[0006] Trace                                         args="[git push origin main]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-app2940891903 operation_name="exec git" time_ms=1696.211958
INFO[0006] Successfully updated the live application spec  application=app2
INFO[0006] Processing results: applications=2 images_considered=2 images_skipped=0 images_updated=2 errors=0

INFO[0127] Starting image update cycle, considering 2 annotated application(s) for update
INFO[0127] Processing results: applications=2 images_considered=2 images_skipped=0 images_updated=0 errors=0

# to view details of an app
kubectl describe -n argocd app root
kubectl describe -n argocd app app1
kubectl describe -n argocd app app2
... ...
  Summary:
    Images:
      nginx:1.12.2
  Sync:
    Compared To:
      Destination:
        Name:       in-cluster
        Namespace:  argocd
      Source:
        Path:             app-of-apps/source/overlays/app2
        Repo URL:         https://github.com/chengfang/image-updater-examples.git
        Target Revision:  main
    Revision:             031e7b8957c3b97af1b528e4098b16ae13126fca
    Status:               Synced
Events:
  Type    Reason              Age   From                           Message
  ----    ------              ----  ----                           -------
  Normal  OperationStarted    19m   argocd-application-controller  Initiated automated sync to 'f83841c207af00092524e447268539e1346ff088'
  Normal  ResourceUpdated     19m   argocd-application-controller  Updated sync status:  -> OutOfSync
  Normal  ResourceUpdated     19m   argocd-application-controller  Updated health status:  -> Missing
  Normal  OperationCompleted  19m   argocd-application-controller  Sync operation to f83841c207af00092524e447268539e1346ff088 succeeded
  Normal  ResourceUpdated     19m   argocd-application-controller  Updated sync status: OutOfSync -> Synced
  Normal  ResourceUpdated     19m   argocd-application-controller  Updated health status: Missing -> Progressing
  Normal  ResourceUpdated     19m   argocd-application-controller  Updated health status: Progressing -> Healthy
  Normal  ImagesUpdated       16m   ArgocdImageUpdater             Successfully updated application 'app2'
  Normal  OperationStarted    16m   argocd-application-controller  Initiated automated sync to '031e7b8957c3b97af1b528e4098b16ae13126fca'
  Normal  ResourceUpdated     16m   argocd-application-controller  Updated sync status: Synced -> OutOfSync
  Normal  ResourceUpdated     16m   argocd-application-controller  Updated sync status: OutOfSync -> Synced
  Normal  OperationCompleted  16m   argocd-application-controller  Sync operation to 031e7b8957c3b97af1b528e4098b16ae13126fca succeeded
  Normal  ResourceUpdated     16m   argocd-application-controller  Updated health status: Healthy -> Progressing
  Normal  ResourceUpdated     15m   argocd-application-controller  Updated health status: Progressing -> Healthy

# to delete the root app
kubectl delete -f app-of-apps/root/root.yaml

```