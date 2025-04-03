# Создаём кластер ClickHouse

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get k8s-sg --folder-name cameda-practicum --format=json | jq -r '.id')
export VERSION=24.8
export ENV=prestable #production
export DATABASE=db1
export USER=cameda
export PASS=P@$$w0rd
```

### Управление кластером с помощью ус.
```
yc clickhouse cluster create \
  --folder-id $FOLDER \
  --name cam-clickhouse \
  --description "Test ClickHouse cluster" \
  --labels cam=clickhouse \
  --environment $ENV \
  --version $VERSION \
  --network-id $NETWORK \
  --security-group-ids $SG \
  --host type=clickhouse,zone-id=$ZONE,subnet-id=$SUBNET,assign-public-ip \
  --user name=$USER,password=$PASS \
  --database name=$DATABASE \
  --clickhouse-resource-preset s3-c4-m16 \
  --clickhouse-disk-type network-ssd \
  --clickhouse-disk-size 101 \
  --cloud-storage=true \
  --websql-access=true \
  --datalens-access=false \
  --enable-sql-database-management=false \
  --enable-sql-user-management=false \
  --metrika-access=false \
  --serverless-access=false \
  --datatransfer-access=false \
  --yandexquery-access=false \
  --service-account $SA \
  --deletion-protection=false \
  --async
```

### Управление кластером с помощью SQL.
```
yc clickhouse cluster create \
  --folder-id $FOLDER \
  --name cam-clickhouse-sql \
  --description "Test ClickHouse cluster with SQL" \
  --labels cam=clickhouse-sql \
  --environment $ENV \
  --version $VERSION \
  --network-id $NETWORK \
  --security-group-ids $SG \
  --host type=clickhouse,zone-id=$ZONE,subnet-id=$SUBNET,assign-public-ip \
  --clickhouse-resource-preset s3-c4-m16 \
  --clickhouse-disk-type network-ssd \
  --clickhouse-disk-size 101 \
  --cloud-storage=true \
  --websql-access=true \
  --datalens-access=false \
  --enable-sql-database-management=true \
  --enable-sql-user-management=true \
  --metrika-access=false \
  --serverless-access=false \
  --datatransfer-access=false \
  --yandexquery-access=false \
  --service-account $SA \
  --deletion-protection=false \
  --admin-password $PASS \
  --async
```
