# Subnet

## Описание.
Подсети создаются в облачных сетях. Созданная подсеть размещается в одной из зон доступности. К подсети можно подключить ресурсы из той же зоны, где находится подсеть.
Ресурсы облака, такие как ВМ, подключаются к подсетям.

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export RT=$(yc vpc route-table get cam-route-table-k8s --folder-name cameda-practicum --format json | jq -r '.id')
```

## Создаём четыре подсети в каждой зоне доступности.
```
yc vpc subnet create \
  --folder-id $FOLDER \
  --name subnet-d \
  --description "Subnet zone d" \
  --network-id $NETWORK \
  --zone ru-central1-d \
  --range 10.143.0.0/24 \
  --async

yc vpc subnet create \
  --folder-id $FOLDER \
  --name subnet-d-for-gateway \
  --description "Subnet zone d for gateway" \
  --route-table-id=$RT \
  --network-id $NETWORK \
  --zone ru-central1-d \
  --range 10.142.0.0/24 \
  --async

yc vpc subnet create \
  --folder-id $FOLDER \
  --name subnet-b \
  --description "Subnet zone b" \
  --network-id $NETWORK \
  --zone ru-central1-b \
  --range 10.141.0.0/24 \
  --async

yc vpc subnet create \
  --folder-id $FOLDER \
  --name subnet-a \
  --description "Subnet zone a" \
  --network-id $NETWORK \
  --zone ru-central1-a \
  --range 10.140.0.0/24 \
  --async
```

## bash
```
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET_GATEWAY=$(yc vpc subnet get subnet-d-for-gateway --folder-name cameda-practicum --format json | jq -r '.id')
```

## Создаём subnet с параметрами dhcp.
```
yc vpc subnet create \
  --name test-subnet \
  --description "My test subnet" \
  --folder-id $FOLDER \
  --network-id $NETWORK \
  --zone $ZONE \
  --range 192.168.0.0/24 \
  --domain-name test.domain \
  --domain-name-server 192.168.0.100 \
  --ntp-server 192.168.0.101
```

## Просмотрим информацию по subnet.
```
yc vpc subnet list-used-addresses $SUBNET
yc vpc subnet get $SUBNET
yc vpc subnet list-operations $SUBNET
```

## Перенесем subnet в другой folder.
```
yc vpc subnet move $SUBNET \
  --destination-folder-id $FOLDER
```
