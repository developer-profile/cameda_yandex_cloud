# Установка External Secret Operator в Managed Kubernetes

## Поехали!
```
export HELM_EXPERIMENTAL_OCI=1 && \
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/external-secrets/chart/external-secrets \
  --version 0.10.5 \
  --untar && \
helm install \
  --namespace external-secret \
  --create-namespace \
  --set-file auth.json=/Users/cameda/practicum/key.json \
  external-secrets ./external-secrets/
```
