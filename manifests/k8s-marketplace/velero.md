# Установка Velero в Managed Kubernetes

## Описание.
Решение для  резервного копирования, восстановления и миграции объектов кластера Managed Service for Kubernetes, в том числе постоянных томов. Velero позволяет:
1. Защитить данные от потери с помощью гибкой системы резервного копирования.
2. Сократить время на восстановление кластера Managed Service for Kubernetes в случае его недоступности.
3. Перенести данные с одного кластера Managed Service for Kubernetes на другой.

## Установка.
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/velero/velero \
     --version 2.30.4-1 \
     --untar && \
helm install \
     --namespace velero \
     --create-namespace \
     --set configuration.backupStorageLocation.bucket=cam-velero \
     --set serviceaccountawskeyvalue_generated.accessKeyID=YCAJE...lM \
     --set serviceaccountawskeyvalue_generated.secretAccessKey=YCMF9xLu6-...-fV \
     velero ./velero/
```
