# Установка FluentBit with CL в Managed Kubernetes

## Описание!
Установим FluentBit с интеграцией в Cloud Logging.

## Поехали!
```
export HELM_EXPERIMENTAL_OCI=1 && \
export LOG-FLUENT=$(yc log group get k8s-fluentbit --folder-name cameda-practicum --format json | jq -r ".id") && \
export K8s=$(yc k8s cluster get cam-kuber-practicum --folder-name cameda-practicum --format json | jq -r ".id") && \
```

```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/fluent-bit/fluent-bit \
  --version 2.1.7-3 \
  --untar && \
helm install \
  --namespace default \
  --set loggingGroupId=$LOG-FLUENT \
  --set loggingFilter=$K8s \
  --set-file auth.json=/Users/cameda/practicum/key.json \
  --set logLevel=error \
fluentbit ./fluent-bit/
```
