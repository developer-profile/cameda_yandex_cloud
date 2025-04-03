# Установка Vault в Managed Kubernetes

## Поехали!
```
export KMS=$(yc kms symmetric-key get k8s-key --folder-name cameda-practicum --format json | jq -r ".id")
```
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/vault/chart/vault \
  --version 0.29.0+yckms \
  --untar && \
helm install \
  --namespace vault \
  --create-namespace \
  --set-file yandexKmsAuthJson=/Users/cameda/practicum/key.json \
  --set yandexKmsKeyId=$KMS \
  --set "injector.enabled=false" \
  --set "csi.enabled=true" \
  hashicorp ./vault/
```

### Инициализируем хранилище.
```
kubectl exec -n vault -ti hashicorp-vault-0 -- vault operator init
```
ОБЯЗАТЕЛЬНО!
Сохраняем Recovery и Root token. Без них хранилище может превратиться в тыкву.
