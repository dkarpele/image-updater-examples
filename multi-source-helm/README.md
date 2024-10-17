This example contains a helm-based multi-source app:
* source 1: nginx chart from helm repository
* source 2: a custom values file in this repo to override the nginx image version to older version

After running image-updater, the image version in the live cluster should be updated to the new version.

## test with multi-source-app
```bash
# to install the multi-source-app Argo CD application as a plain manifest
kubectl apply -f multi-source-helm/app/multi-source-app.yaml

# to view the application pod
kubectl get pod/multi-source-helm-nginx-75b44767bb-gtnnd -n argocd -o yaml

# to view the current container image name and image tag
kubectl get pod/multi-source-helm-nginx-75b44767bb-gtnnd -n argocd -o jsonpath='{.spec.containers[0].image}'
docker.io/bitnami/nginx:1.27.1

# to run image-updater from command line
../image-updater/dist/argocd-image-updater run --once --registries-conf-path=""
INFO[0000] argocd-image-updater v99.9.9+02eee1d starting [loglevel:INFO, interval:once, healthport:off]
WARN[0000] commit message template at /app/config/commit.template does not exist, using default
INFO[0000] ArgoCD configuration: [apiKind=kubernetes, server=argocd-server.default, auth_token=false, insecure=false, grpc_web=false, plaintext=false]
INFO[0000] Starting metrics server on TCP port=8081
INFO[0000] Warming up image cache
INFO[0000] Setting new image to docker.io/bitnami/nginx:1.27.2  alias=nginx application=multi-source-helm image_name=bitnami/nginx image_tag=1.27.1 registry=docker.io
INFO[0000] Successfully updated image 'docker.io/bitnami/nginx:1.27.1' to 'docker.io/bitnami/nginx:1.27.2', but pending spec update (dry run=true)  alias=nginx application=multi-source-helm image_name=bitnami/nginx image_tag=1.27.1 registry=docker.io
INFO[0000] Dry run - not committing 1 changes to application  application=multi-source-helm
INFO[0000] Finished cache warm-up, pre-loaded 0 meta data entries from 1 registries
INFO[0000] Starting image update cycle, considering 1 annotated application(s) for update
INFO[0001] Setting new image to docker.io/bitnami/nginx:1.27.2  alias=nginx application=multi-source-helm image_name=bitnami/nginx image_tag=1.27.1 registry=docker.io
INFO[0001] Successfully updated image 'docker.io/bitnami/nginx:1.27.1' to 'docker.io/bitnami/nginx:1.27.2', but pending spec update (dry run=false)  alias=nginx application=multi-source-helm image_name=bitnami/nginx image_tag=1.27.1 registry=docker.io
INFO[0001] Committing 1 parameter update(s) for application multi-source-helm  application=multi-source-helm
INFO[0001] Successfully updated the live application spec  application=multi-source-helm
INFO[0001] Processing results: applications=1 images_considered=1 images_skipped=0 images_updated=1 errors=0
INFO[0001] Finished.

# to check the updated image tag
kubectl get pod multi-source-helm-nginx-598b65b7bd-s854v -n argocd -o jsonpath='{.spec.containers[0].image}'
docker.io/bitnami/nginx:1.27.2

# to delete the multi-source-app
kubectl delete -f multi-source-helm/app/multi-source-app.yaml 
application.argoproj.io "multi-source-helm" deleted
```