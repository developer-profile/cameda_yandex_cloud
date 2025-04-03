# Установка кластера Managed Airflow

## Описание.
Airflow - одна из самых быстрых и мощных ETL систем.

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cam-airflow --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get k8s-sg --folder-name cameda-practicum --format=json | jq -r '.id')
export BUCKET=cam-flow
export PASS=P@$$w0rd
```

## Create cluster with triggerer.
```
yc managed-airflow cluster create \
   --folder-id $FOLDER \
   --name cam-airflow \
   --description "Airflow test cluster" \
   --labels cam=airflow \
   --admin-password $PASS \
   --service-account-id $SA \
   --subnet-ids $SUBNET \
   --security-group-ids $SG \
   --webserver count=1,resource-preset-id=c1-m4 \
   --scheduler count=1,resource-preset-id=c1-m4 \
   --worker min-count=1,max-count=2,resource-preset-id=c2-m8 \
   --triggerer count=1,resource-preset-id=c2-m8 \
   --pip-packages yandexcloud==0.298.0,clickhouse-connect,datasphere \
   --dags-bucket $BUCKET \
   --deletion-protection=false \
   --lockbox-secrets-backend=false \
   --airflow-config webserver.expose_config=true \
   --log-enabled=false \
   --log-folder-id $FOLDER \
   --log-min-level error \
   --async
```
