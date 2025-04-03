# IAM

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Создадим SA в фолдере cameda-practicum.
```
yc iam service-account create \
   --folder-id $FOLDER \
   --name cameda-practicum \
   --description "Main Practicum SA" \
   --async
```

## Зададим для этого SA права editor на фолдер cameda-practicum.
```
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
yc resource folder add-access-binding $FOLDER --role editor --subject serviceAccount:$SA
```

## Проверим, что права на фолдер для SA действительно установились.
```
yc resource folder list-access-bindings $FOLDER | grep $SA
```

## Создаём ключи для сервисного аккаунта.

### Создадим на рабочей машине директорию с ключами доступа для SA.
```
mkdir practicum
```

### IAM token.
```
yc iam key create --service-account-id $SA --folder-name cameda-practicum --output practicum/key.json
```

### Создадим статический ключ доступа для возможности работы с Object Storage.
```
yc iam access-key create --service-account-id $SA --folder-name cameda-practicum --format=json > practicum/stat-key-to-s3.json
```

### Создадим api ключ для постоянного доступа.
```
yc iam api-key create --service-account-id $SA --folder-name cameda-practicum > practicum/api-key.yaml
```
