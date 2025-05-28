# Установка кластера Managed Airflow

## Описание.
Airflow - одна из самых быстрых и мощных ETL систем.

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get cam-all --folder-name cameda-practicum --format=json | jq -r '.id')
export BUCKET=cam-flow
export PASS=alIjd$gnT3kxmH?nq11
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

## Create cluster without triggerer.
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

* Пароль echo $PASS

## Create cluster wiht Lockbox and SMTP .
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d-for-gateway --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get cam-all --folder-name cameda-practicum --format=json | jq -r '.id')
export BUCKET=cam-flow
export PASS=alIjd$gnT3kxmH?nq11

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
   --pip-packages yandexcloud==0.298.0,clickhouse-connect,datasphere \
   --airflow-config smtp.smtp_host=postbox.cloud.yandex.net,smtp.smtp_port=587,smtp.smtp_starttls=True,smtp.smtp_ssl=False,smtp.smtp_mail_from=mail@cameda1.ru \
   --airflow-config webserver.expose_config=true \
   --dags-bucket $BUCKET \
   --deletion-protection=false \
   --lockbox-secrets-backend=true \
   --log-enabled=false \
   --log-folder-id $FOLDER \
   --log-min-level error \
   --async
```
* Пароль echo $PASS
