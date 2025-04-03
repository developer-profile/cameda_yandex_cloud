# Примеры создания нод групп

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get k8s-sg --folder-name cameda-practicum --format=json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET1=$(yc vpc subnet get subnet-b --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET2=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export VERSION=1.30
```

## Создание фиксированной группы с нодами в разных зонах доступности. Без внешнего адреса.
```
yc k8s node-group create \
--folder-id $FOLDER \
--name cam-fixed-zonal-much-zone \
--cluster-name cam-kuber-test \
--description "fixed group for test" \
--metadata serial-port-enable=1 \
--metadata-from-file=ssh-keys=/Users/cameda/ssh-pairs.txt \
--labels cam=zonal \
--node-labels env=deploy \
--location zone=$ZONE,subnet-id=$SUBNET \
--location zone=ru-central1-b,subnet-name=subnet-b \
--platform standard-v3 \
--memory 16 \
--cores 8 \
--core-fraction 100 \
--disk-type network-ssd \
--disk-size 96 \
--network-acceleration-type standard \
--version $VERSION \
--container-runtime containerd \
--fixed-size 2 \
--auto-upgrade=true \
--auto-repair \
--max-expansion 1 \
--max-unavailable 1 \
--daily-maintenance-window 'start=22:00,duration=5h' \
--allowed-unsafe-sysctls net.ipv4.tcp_timestamps \
--async
```

## Создание фиксированной группы с нодами в разных зонах доступности. С внешним адресом.
```
yc k8s node-group create \
--folder-id $FOLDER \
--name cam-fixed-zonal-much-zone \
--cluster-name cam-kuber-test \
--description "fixed group for test" \
--metadata serial-port-enable=1 \
--metadata-from-file=ssh-keys=/Users/cameda/ssh-pairs.txt \
--labels cam=zonal \
--node-labels env=deploy \
--location zone=$ZONE \
--location zone=ru-central1-b \
--platform standard-v3 \
--memory 16 \
--cores 8 \
--core-fraction 100 \
--disk-type network-ssd \
--disk-size 96 \
--network-acceleration-type standard \
--network-interface security-group-ids=$SG,subnets=[$SUBNET1,$SUBNET2],ipv4-address=nat \
--version $VERSION \
--container-runtime containerd \
--fixed-size 2 \
--auto-upgrade=true \
--auto-repair \
--max-expansion 1 \
--max-unavailable 1 \
--daily-maintenance-window 'start=22:00,duration=5h' \
--allowed-unsafe-sysctls net.ipv4.tcp_timestamps \
--async
```

## Create 2 worker node. Fixed node group.
```
yc k8s node-group create \
--folder-id $FOLDER \
--name cam-fixed-zonal \
--cluster-name cam-kuber-zonal \
--description "fixed group for test" \
--metadata serial-port-enable=1 \
--metadata-from-file=ssh-keys=/Users/cameda/ssh-pairs.txt \
--labels cam=zonal \
--node-labels env=deploy \
--location zone=$ZONE \
--platform standard-v3 \
--memory 16 \
--cores 8 \
--core-fraction 100 \
--disk-type network-ssd \
--disk-size 96 \
--network-acceleration-type standard \
--network-interface security-group-ids=$SG,subnets=$SUBNET,ipv4-address=nat \
--version $VERSION \
--container-runtime containerd \
--fixed-size 2 \
--auto-upgrade=true \
--auto-repair \
--max-expansion 1 \
--max-unavailable 1 \
--daily-maintenance-window 'start=22:00,duration=5h' \
--allowed-unsafe-sysctls net.ipv4.tcp_timestamps \
--async
```
