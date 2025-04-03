# Установка кластера Opensearch 

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get cam-db --folder-name cameda-practicum --format=json | jq -r '.id')
export ENV=prestable #production
export VERSION=2.17
```

### Создание кластера.
```
yc managed-opensearch cluster create \
   --folder-id $FOLDER \
   --name cam-opensearch \
   --description "Test Opensearch cluster" \
   --labels cam=test \
   --environment $ENV \
   --network-id $NETWORK \
   --security-group-ids $SG \
   --service-account-id $SA \
   --delete-protection=false \
   --maintenance schedule=weekly,weekday=sat,hour=11 \
   --version $VERSION \
   --read-admin-password \
   --data-transfer-access=false \
   --serverless-access=false \
   --async \
   --plugins mapper-murmur3,yandex-lemmer \
   --opensearch-node-group name=cam-os-node,`
                          `resource-preset-id=s3-c2-m8,`
                          `disk-size=17179869184,`
                          `disk-type-id=network-ssd,`
                          `hosts-count=1,`
                          `zone-ids=$ZONE,`
                          `subnet-ids=$SUBNET,`
                          `assign-public-ip=true,`
                          `roles=data+manager \
   --dashboards-node-group name=cam-os-dash,`
                          `resource-preset-id=s3-c2-m8,`
                          `disk-size=17179869184,`
                          `disk-type-id=network-ssd,`
                          `hosts-count=1,`
                          `zone-ids=$ZONE,`
                          `subnet-ids=$SUBNET,`
                          `assign-public-ip=true
```

Размер диска указываем в байтах. В примере 16Гб.
