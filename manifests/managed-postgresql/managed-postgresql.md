# Установка кластера PostgreSQL

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get cam-db --folder-name cameda-practicum --format=json | jq -r '.id')
export ENV=prestable #production
export VERSION=17
export DATABASE=db1
export USER=cameda
export PASS=P@$$w0rd
```

### Создание стандартного кластера.
```
yc postgresql cluster create \
 --folder-id $FOLDER \
 --name cam-postgres \
 --description 'PostgreSQL main' \
 --postgresql-version $VERSION \
 --environment $ENV \
 --labels prod=postgres \
 --network-id $NETWORK \
 --resource-preset s3-c4-m16 \
 --host zone-id=$ZONE,subnet-id=$SUBNET,assign-public-ip \
 --disk-type network-ssd \
 --disk-size 67 \
 --user name=$USER,password=$PASS,conn-limit=50 \
 --database name=$DATABASE,owner=$USER \
 --security-group-ids $SG \
 --backup-window-start 01:00:00 \
 --backup-retain-period-days 7 \
 --websql-access=true \
 --serverless-access=false \
 --datalens-access=false \
 --datatransfer-access=false \
 --deletion-protection=false \
 --async
```

### Вариант адаптированный под 1С
export VERSION=17-1c
```
yc postgresql cluster create \
 --folder-id $FOLDER \
 --name cam-postgres \
 --description 'PostgreSQL main' \
 --postgresql-version $VERSION \
 --environment $ENV \
 --labels prod=postgres \
 --network-id $NETWORK \
 --resource-preset s3-c4-m16 \
 --host zone-id=$ZONE,subnet-id=$SUBNET,assign-public-ip \
 --disk-type network-ssd \
 --disk-size 67 \
 --user name=$USER,password=$PASS,conn-limit=50 \
 --database name=$DATABASE,owner=$USER \
 --security-group-ids $SG \
 --backup-window-start 01:00:00 \
 --backup-retain-period-days 7 \
 --websql-access=true \
 --serverless-access=false \
 --datalens-access=false \
 --datatransfer-access=false \
 --deletion-protection=false \
 --async
```
