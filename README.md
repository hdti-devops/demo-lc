# demo-lc

Demo pour Helm & Kustomize avec ArgoCD.

## Prerequis

* kubectl
* minikube
* Helm
* kustomize

## ArgoCd 

Par bonne pratique et aussi pour faciliter les futures modifications dont j'aurai besoin dans cet exemple, je vais utiliser la technique de 'make ArgoCD mange itself'.
</br>
*La Chart de ArgoCD avec la version LC est deja decompresse dans mon dossier manifests. On peu voir ce dossier comme notre Artifactorysur nos environnements.*
</br></br>
Pour debuter, nous devrons intaller ArgoCD pour qu'on puisse ensuite le faire pointer sur notre depot. Nous ferons ceci a l'aide de kustomize. Il faut aussi prendre en considerationque vu que je vais le deployer avec kustomize, il est donc necessaire de creer le fichier pour le namespace vu que kustomize ne dispose pas de la fonction --create-namespace.

*argocd/namespace.yaml*
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: argocd
  labels:
    name: argocd
```

*argocd/kustomization.yaml*
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: argocd

helmGlobals:
  chartHome: '../../manifests'

helmCharts:
  - name: 'argo-cd'
    releaseName: argocd
    namespace: argocd
    version: 6.10.2
    includeCRDs: true

resources:
- namespace.yaml
```

Pour commencer,je vais sauvegarder la configuration des templates initiale que je vais store dans ```apps/argocd/base-manifest.yaml```. *Cette etape est optionnelle*
```kustomize build --enable-helm --load-restrictor=LoadRestrictionsNone . > ./base-manifest/argocd.yaml```.

Ensuite, je deploie ArgoCD avec ```kustomize build --enable-helm --load-restrictor=LoadRestrictionsNone . | k apply -f -```.

Je recupere le password: ```kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo``` et port-forward mon argocd-server ``` k port-forward service/argocd-server -n argocd 8080:80```