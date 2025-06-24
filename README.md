# Selenium GitOps with Argo CD

Este repositorio contiene ejemplos prácticos para desplegar un cluster de **Selenium Hub** con un nodo de **Chrome** en Kubernetes utilizando **Argo CD**, en tres modalidades distintas:

- **YAML básico**
- **Kustomize**
- **Helm**

## 🔖 Contenido del Repositorio

```text
selenium-gitops/
├── charts/
│   └── selenium-grid/             # Helm Chart
├── kustomize/
│   ├── base/                      # Manifiestos base para Kustomize
│   └── overlays/
│       └── default/               # Overlays para personalizaciones
└── manifests/                     # YAML puro
```

## 🚀 Pre-requisitos

- **Kubernetes** (Minikube, Kind, MicroK8s, o WSL2)
- **Argo CD** instalado en tu cluster Kubernetes
- **kubectl** configurado localmente

## 📝 Modalidades

### 1. YAML Básico

Manifiestos sencillos para despliegue manual o mediante Argo CD.

**Ruta**: `manifests/`

### 2. Kustomize

Uso de Kustomize para facilitar personalizaciones y gestión en múltiples entornos.

**Ruta**: `kustomize/`

### 3. Helm

Chart oficial de Selenium Grid personalizado para simplificar despliegues avanzados.

**Ruta**: `charts/selenium-grid/`

## 🎯 Uso con Argo CD

Cada carpeta contiene ejemplos de configuración de **Application** para integrar automáticamente con Argo CD:

- YAML puro → apunta a `manifests/`
- Kustomize → apunta a `kustomize/overlays/default`
- Helm → apunta a `charts/selenium-grid`

Ejemplo básico de Argo CD Application:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: selenium-hub
spec:
  project: default
  source:
    repoURL: https://github.com/nexusadobo/selenium-gitops
    path: manifests  # O cambiar según modalidad
    targetRevision: HEAD
  destination:
    server: https://kubernetes.default.svc
    namespace: selenium
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## 💻 Limitaciones y Recomendaciones

Este repositorio está pensado para entornos limitados como WSL2, utilizando solo **un nodo de Chrome** para minimizar el uso de recursos.

- **Recursos mínimos recomendados**: 2 vCPU y 4 GB RAM.
- Puedes ampliar fácilmente los recursos y réplicas en `values.yaml` o Kustomize según sea necesario.

## 📚 Documentación adicional

- [Argo CD Example Apps](https://github.com/argoproj/argocd-example-apps)
- [Selenium Grid Helm Chart](https://artifacthub.io/packages/helm/selenium-grid/selenium-grid)
- [Selenium GitHub Repo](https://github.com/SeleniumHQ/selenium)

## 🛠️ Autor

Creado por [@nexusadobo](https://github.com/nexusadobo).

