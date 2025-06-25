 ## Instalar ArgoCD

 ```sh
 # Crear namespace
kubectl create namespace argocd

# Instalar ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Esperar a que todos los pods estén listos
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```

 ## Acceder a la UI de ArgoCD

```sh
  # Port-forward para acceder desde el navegador
kubectl port-forward svc/argocd-server -n argocd 8080:443 &

# Obtener la contraseña inicial del admin
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
  
```

## Instalar ArgoCD Using a Helm Chart
```sh
helm repo add argo https://argoproj.github.io/argo-helm

helm install argocd-demo argo/argo-cd -f argocd-custom-values.yaml

# argocd-custom-values.yaml
server:
  service:
    type: NodePort
kubectl get pods -n argocd    
  
```
## Crear una aplicación de ejemplo
```sh

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true