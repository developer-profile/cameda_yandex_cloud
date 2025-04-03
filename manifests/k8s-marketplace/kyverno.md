# Установка Kyverno в Managed Kubernetes

## Описание.
Установка Kyverno в Yandex Cloud.

## Поехали!
```
helm pull oci://cr.yandex/yc-marketplace/multi-kyverno \
  --version 1.0.0 \
  --untar && \
helm install \
  --namespace default \
  --create-namespace \
  multi-kyverno ./multi-kyverno/
```
