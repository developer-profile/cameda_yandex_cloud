# Шлюз

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Зарезервируем адрес в зоне ru-central1-d.
```
yc vpc gateway create \
  --folder-id $FOLDER \
  --name cam-gateway \
  --description "BASIC gateway" \
  --async 
```
