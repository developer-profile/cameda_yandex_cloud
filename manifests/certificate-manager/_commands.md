# Команды Certificate Manager

## BASH
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export CERT=$(yc cm certificate get cam-cert --folder-name cameda-practicum --format=json | jq -r ".id")
```

## Basic commands.
```
yc certificate-manager certificate list --folder-id $FOLDER
yc certificate-manager certificate get $CERT
yc certificate-manager certificate content $CERT
yc certificate-manager certificate list-access-bindings $CERT
yc certificate-manager certificate list-operations $CERT
```

## Запрос сертификата для домена.
```
yc certificate-manager certificate request \
  --folder-id $FOLDER \
  --name kube-infra \
  --domains "*.cameda1.ru" \
  --challenge dns
```

## Создание пользовательского сертификата.
https://github.com/Cameda/Yandex-Cloud/blob/main/manifests/certificate-manager/01.create_user_cert.md
