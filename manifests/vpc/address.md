# Адрес

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
```

## Зарезервируем адрес в зоне ru-central1-d.
```
yc vpc address create \
   --folder-id $FOLDER \
   --name main-vm \
   --description "Main VM" \
   --external-ipv4 zone=$ZONE \
   --labels cameda=prod \
   --async
```

## bash
```
export IP=$(yc vpc address get main-vm --folder-name cameda-practicum  --format=json | jq -r ".external_ipv4_address" | jq -r ".address")
```

## Просмотрим информацию об адресе.
```
yc vpc address get $IP
yc vpc address list-operations $IP
```
