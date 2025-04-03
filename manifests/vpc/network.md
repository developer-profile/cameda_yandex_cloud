# Network

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Создаем сеть.
```
yc vpc network create \
   --folder-id $FOLDER \
   --name cameda-practicum \
   --description "Cameda Practicum network" \
   --labels cameda=practicum \
   --async
```

## bash
```
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
```

## Посмотрим информацию о сетях.
```
yc vpc network get $NETWORK
yc vpc network list-subnets $NETWORK
yc vpc network list-security-groups $NETWORK
yc vpc network list-route-tables $NETWORK
yc vpc network list-route-operations $NETWORK
```
