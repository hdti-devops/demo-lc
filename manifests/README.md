# Manifests

Ici seront les Helm Chats que je vais utiliser.

* ArgoCd v.LC (6.10.2)
```
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm pull argo/argo-cd --version 6.10.2 --untar 
```

* OpenEBS v.LC
``` ```