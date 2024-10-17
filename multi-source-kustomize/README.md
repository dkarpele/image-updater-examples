This example contains a single-source app and a multi-source app, both using the same 
`deployment.yaml`. You can compare the observe how image-updater works for these 2 types
of applications.

## test with multi-source-app
```bash
# to install the multi-source-app Argo CD application as a plain manifest
kubectl apply -f multi-source-kustomize/app/multi-source-app.yaml

# to view the application pod
kubectl get pod -l app=multi-source-kustomize -n argocd
NAME                                     READY   STATUS    RESTARTS   AGE
multi-source-kustomize-6965f6974-vn7mw   1/1     Running   0          9m22s

# to view the current container image name and image tag
kubectl get pod -l app=multi-source-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.0

# to run image-updater from command line
../image-updater/dist/argocd-image-updater run --once --registries-conf-path=""
INFO[0000] argocd-image-updater v99.9.9+02eee1d starting [loglevel:INFO, interval:once, healthport:off]
WARN[0000] commit message template at /app/config/commit.template does not exist, using default
INFO[0000] ArgoCD configuration: [apiKind=kubernetes, server=argocd-server.default, auth_token=false, insecure=false, grpc_web=false, plaintext=false]
INFO[0000] Starting metrics server on TCP port=8081
INFO[0000] Warming up image cache
INFO[0000] Setting new image to nginx:1.16.1             alias=nginx application=multi-source-kustomize image_name=nginx image_tag=1.16.0 registry=
INFO[0000] Successfully updated image 'nginx:1.16.0' to 'nginx:1.16.1', but pending spec update (dry run=true)  alias=nginx application=multi-source-kustomize image_name=nginx image_tag=1.16.0 registry=
INFO[0000] Dry run - not committing 1 changes to application  application=multi-source-kustomize
INFO[0000] Finished cache warm-up, pre-loaded 0 meta data entries from 1 registries
INFO[0000] Starting image update cycle, considering 1 annotated application(s) for update
INFO[0000] Setting new image to nginx:1.16.1             alias=nginx application=multi-source-kustomize image_name=nginx image_tag=1.16.0 registry=
INFO[0000] Successfully updated image 'nginx:1.16.0' to 'nginx:1.16.1', but pending spec update (dry run=false)  alias=nginx application=multi-source-kustomize image_name=nginx image_tag=1.16.0 registry=
INFO[0000] Committing 1 parameter update(s) for application multi-source-kustomize  application=multi-source-kustomize
INFO[0000] Successfully updated the live application spec  application=multi-source-kustomize
INFO[0000] Processing results: applications=1 images_considered=1 images_skipped=0 images_updated=1 errors=0
INFO[0000] Finished.

# to check the updated image tag
get pod -l app=multi-source-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.1

# to delete the multi-source-app
kubectl delete -f multi-source-kustomize/app/multi-source-app.yaml
application.argoproj.io "multi-source-kustomize" deleted
```
## test with single-source-app
```bash
# to install single-source-app Argo CD application
kubectl apply -f multi-source-kustomize/app/single-source-app.yaml

# to view the current container image name and image tag
kubectl get pod -l app=multi-source-kustomize -n argocd -o jsonpath='{.items[0].spec.containers[0].image}'
nginx:1.16.0

# to run image-updater from command line
../image-updater/dist/argocd-image-updater run --once --registries-conf-path=""

# to delete the app
kubectl delete -f multi-source-kustomize/app/single-source-app.yaml 
```