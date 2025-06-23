# Selenium Grid GitOps con ArgoCD y Helm

Este proyecto despliega un entorno de Selenium Grid en Kubernetes utilizando Helm charts y gestiona el ciclo de vida de la aplicación con ArgoCD siguiendo prácticas de GitOps.

## Estructura del repositorio

- `apps/selenium-grid/`: Helm chart para desplegar Selenium Grid.
- `argocd-apps/selenium-app.yaml`: Definición de la aplicación ArgoCD para Selenium Grid.
- `demo-argo/`: Ejemplo de aplicación gestionada por ArgoCD.

## Requisitos

- Kubernetes cluster
- [Helm](https://helm.sh/)
- [ArgoCD](https://argo-cd.readthedocs.io/)

## Instalación

### 1. Instalar ArgoCD

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```