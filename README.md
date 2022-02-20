## argocd-apps

This repo shows a brief demo about using kustomize to patch argocd apps with a new approach.


#### Install argocd

First, If you don't have argocd yet running, please proceed to install it.

#### Update CRD

```
kubectl apply -f base/crds/application-crd.yaml
```

#### Update argocd image:

```
kubectl -n argocd patch deploy argocd-server -p 'spec:
    template:
      spec:
        containers:
         - name: argocd-server
           image: medchiheb/argocd-extra-values:alpha
           imagePullPolicy: IfNotPresent'

kubectl -n argocd patch deploy argocd-repo-server -p 'spec:
    template:
      spec:
        initContainers:
         - name: copyutil
           image: medchiheb/argocd-extra-values:alpha
           imagePullPolicy: IfNotPresent
        containers:
         - name: argocd-repo-server
           image: medchiheb/argocd-extra-values:alpha
           imagePullPolicy: IfNotPresent'

kubectl -n argocd patch deploy argocd-dex-server -p 'spec:
    template:
      spec:
        initContainers:
         - name: copyutil
           image: medchiheb/argocd-extra-values:alpha
           imagePullPolicy: IfNotPresent
        containers:
         - name: argocd-repo-server
           image: medchiheb/argocd-extra-values:alpha
           imagePullPolicy: IfNotPresent'

kubectl -n argocd patch sts argocd-application-controller -p 'spec:
    template:
      spec:
        containers:
         - name: argocd-application-controller
           image: medchiheb/argocd-extra-values:alpha
           imagePullPolicy: IfNotPresent'
```      


#### Test

```
kubectl -n argocd apply -f overlays/dev/root.yaml
kubectl -n argocd apply -f overlays/prod/root.yaml
```


#### Clean



```
kubectl -n argocd rollout undo deployment argocd-server
kubectl -n argocd rollout undo deployment argocd-repo-server
kubectl -n argocd rollout undo deployment argocd-dex-server
kubectl -n argocd rollout undo statefulset argocd-application-controller
```     