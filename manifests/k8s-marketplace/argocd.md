# Установка ArgoCD в Managed Kubernetes

## Установка.
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/argo/chart/argo-cd \
  --version 7.3.11-2 \
  --untar && \
helm install \
  --namespace argocd \
  --create-namespace \
  argo-cd ./argo-cd/
```

```
login: admin
password:
kubectl --namespace argocd get secret argocd-initial-admin-secret \
  --output jsonpath="{.data.password}" | base64 -d
```
