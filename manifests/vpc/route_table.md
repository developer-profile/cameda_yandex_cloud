# Route table

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export GATEWAY=$(yc vpc gateway get cam-gateway --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d-for-gateway --folder-name cameda-practicum --format json | jq -r '.id')
```

## Поехали!
```
yc vpc route-table create \
  --folder-id=$FOLDER \
  --name=cam-route-table-k8s \
  --description "Cameda RT for k8s cluster node and oher test" \
  --labels cam=k8s-route-node \
  --network-id=$NETWORK \
  --route destination=0.0.0.0/0,gateway-id=$GATEWAY \
  --async
```

## bash
```
export RT=$(yc vpc route-table get cam-route-table-k8s --folder-name cameda-practicum --format json | jq -r '.id')
```
