# Cloud DNS

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Создаём публичную зону в Cloud DNS.

```
yc dns zone create \
  --folder-id $FOLDER \
  --name k8s-ingress \
  --description "deploy gitops model" \
  --zone cameda1.ru. \
  --public-visibility \
  --async
```

## Создаём А запись и связываем её с адресом, созданным ранее в VPC.

```
yc dns zone add-records --name k8s-ingress \
  --folder-id $FOLDER \
  --record "*.cameda1.ru. 600 A $IP"
```

## Добавим CNAME запись в домен для прохождения валидации.

```
yc dns zone add-records \
  --name k8s-ingress \
  --folder-id $FOLDER \
  --record "_acme-challenge.cameda1.ru. 600 CNAME $CERT.cm.yandexcloud.net."
```
