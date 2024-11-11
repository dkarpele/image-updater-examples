This example contains a helm-based multi-source app:
* source 1: nginx chart from helm repository
* source 2: a custom values file in this repo to override the nginx image version to older version

After running image-updater, the image version in the live cluster should be updated to the new version.
The changes are written to `source2/values.yaml` file. See commit history of source2 repo for diffs

## test with write-helmvalues
```bash
# to create the secret to access the git repo
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install the write-helmvalues Argo CD application as a plain manifest
kubectl apply -f write-helmvalues/app/ write-helmvalues.yaml

# to view the application pod
kubectl get pod/ write-helmvalues-nginx-xxx -n argocd -o yaml

# to view the current container image name and image tag
kubectl get pod/ write-helmvalues-nginx-75b44767bb-gtnnd -n argocd -o jsonpath='{.spec.containers[0].image}'
docker.io/bitnami/nginx:1.27.1

# to run image-updater from command line
../image-updater/dist/argocd-image-updater run --once --registries-conf-path=""

# to check the updated image tag
kubectl get pod  write-helmvalues-nginx-598b65b7bd-s854v -n argocd -o jsonpath='{.spec.containers[0].image}'
docker.io/bitnami/nginx:1.27.2

# to delete the write-helmvalues app
kubectl delete -f  write-helmvalues/app/ write-helmvalues.yaml 
application.argoproj.io " write-helmvalues" deleted
```