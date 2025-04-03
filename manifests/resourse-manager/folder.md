# Resource Manager

## Создаём фолдер в облаке.

```
yc resource folder create \
  --name cameda-practicum \
  --description "It is folder for test Practicum lessons" \
  --labels cameda=practicum \
  --async
```

## Проверим, что фолдер успешно создался.

```
yc resource folder list | grep cameda-practicum
```

## Посмотрим информацию о фолдере.

```
yc resource folder get cameda-practicum
yc resource folder list-operations cameda-practicum
yc resource folder list-access-bindings cameda-practicum
```

## bash

```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```
