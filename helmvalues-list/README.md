This example contains a helm-based multi-source app:
* source 1: nginx chart from helm repository
* source 2: a custom values file in this repo to override the nginx image version to older version

After running image-updater, the image version in the live cluster should be updated to the new version.
The changes are written to `helmvalues-list/source2/values.yaml` file. See commit history of source2 repo for diffs
```shell
  -  repository: bitnami/nginx
  -  tag: 1.27.1
  
  +  repository: docker.io/bitnami/nginx
  +  tag: 1.27.2
```
To configure the app to write updates to the correct repo, branch, values file path and values elements,
the following application annotations are required:
```yaml
argocd-image-updater.argoproj.io/nginx.helm.image-name: image.repository
argocd-image-updater.argoproj.io/nginx.helm.image-tag: image.tag
argocd-image-updater.argoproj.io/git-repository: https://github.com/chengfang/image-updater-examples.git
argocd-image-updater.argoproj.io/git-branch: main
argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/git-creds
argocd-image-updater.argoproj.io/write-back-target: helmvalues:/helmvalues-list/source2/values.yaml
```

To configure git write-back to use the default target file, `.argocd-source-<appName>.yaml`,
remove the following line from application annotations:
```yaml
argocd-image-updater.argoproj.io/write-back-target: helmvalues
```

To configure the application to use the default write-back-method, `argocd`, remove these lines
from application annotations. With `argocd` write-back-method, no updates will be committed to
the git repository.
```yaml
argocd-image-updater.argoproj.io/git-repository: https://github.com/chengfang/image-updater-examples.git
argocd-image-updater.argoproj.io/git-branch: main
argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/git-creds
argocd-image-updater.argoproj.io/write-back-target: helmvalues:/helmvalues-list/source2/values.yaml
```

## test with helmvalues-list
```bash
# to create the secret to access the git repo
kubectl -n argocd create secret generic git-creds --from-literal=username=xxx --from-literal=password=xxx

# to install the helmvalues-list Argo CD application as a plain manifest
kubectl apply -f helmvalues-list/app/helmvalues-list.yaml

# to view the application pod
kubectl get pod helmvalues-list-nginx-xxx -n argocd -o yaml

# to view the current container image name and image tag
kubectl get pod helmvalues-list-nginx-75b44767bb-gtnnd -n argocd -o jsonpath='{.spec.containers[0].image}'
docker.io/bitnami/nginx:1.27.1

# to run image-updater from command line
../image-updater/dist/argocd-image-updater run --once --registries-conf-path=""
INFO[0001] Committing 1 parameter update(s) for application helmvalues-list  application=helmvalues-list
INFO[0001] Initializing https://github.com/chengfang/image-updater-examples.git to /var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916
INFO[0001] git fetch origin main --force --prune --depth 1  dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=496ba
INFO[0002] Trace                                         args="[git fetch origin main --force --prune --depth 1]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=377.96333300000003
INFO[0002] git checkout --force main                     dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=4464b
INFO[0002] Trace                                         args="[git checkout --force main]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=21.391458
INFO[0002] git clean -ffdx                               dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=de99f
INFO[0002] Trace                                         args="[git clean -ffdx]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=7.692958
INFO[0002] git config user.name argocd-image-updater     dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=2e12f
INFO[0002] Trace                                         args="[git config user.name argocd-image-updater]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=6.537084
INFO[0002] git config user.email noreply@argoproj.io     dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=7a95d
INFO[0002] Trace                                         args="[git config user.email noreply@argoproj.io]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=5.877333
INFO[0002] git -c gpg.format=openpgp commit -a -F /var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/image-updater-commit-msg2331629386  dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=a8964
INFO[0002] Trace                                         args="[git -c gpg.format=openpgp commit -a -F /var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/image-updater-commit-msg2331629386]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=19.030917000000002
INFO[0002] git push origin main                          dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 execID=d8f75
INFO[0005] Trace                                         args="[git push origin main]" dir=/var/folders/sc/bblzy4t15995h2zs1s4bztc00000gn/T/git-helmvalues-list2760154916 operation_name="exec git" time_ms=3038.371375
INFO[0005] Successfully updated the live application spec  application=helmvalues-list

# to check the updated image tag
kubectl get pod helmvalues-list-nginx-xxx -n argocd -o jsonpath='{.spec.containers[0].image}'
docker.io/bitnami/nginx:1.27.2

# to delete the helmvalues-list app
kubectl delete -f helmvalues-list/app/helmvalues-list.yaml 
application.argoproj.io "helmvalues-list" deleted
```