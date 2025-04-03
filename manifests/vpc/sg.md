# Security Group

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
```

## Стандартная SG, подходит для тестовой ВМ.
```
yc vpc sg create --name cam-default \
"--rule" "description=access all egress port,direction=egress,from-port=1,to-port=65535,protocol=any,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 22 port,direction=ingress,port=22,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 53 port,direction=ingress,port=53,protocol=any,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 80 port,direction=ingress,port=80,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 443 port,direction=ingress,port=443,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 8080 port,direction=ingress,port=8080,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
--network-id $NETWORK --description "Default access" --folder-id $FOLDER --async
```

## Всем всё!
```
yc vpc sg create --name cam-all \
"--rule" "description=access all egress port,direction=egress,from-port=0,to-port=65535,protocol=any,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access all ingress port,direction=ingress,from-port=0,to-port=65535,protocol=any,v4-cidrs=[0.0.0.0/0]" \
--network-id $NETWORK --description "All access" --folder-id $FOLDER --async
```

## SG для работы Kubernetes.
```
yc vpc sg create --name k8s-sg \
"--rule" "description=access all egress port,direction=egress,from-port=1,to-port=65535,protocol=any,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 22 port,direction=ingress,port=22,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 443 port,direction=ingress,port=443,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 6443 port,direction=ingress,port=6443,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=balancer port,direction=ingress,from-port=1,to-port=65535,protocol=tcp,v4-cidrs=[198.18.235.0/24],v4-cidrs=[198.18.248.0/24]" \
"--rule" "description=service port,direction=ingress,from-port=30000,to-port=32767,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=ICMP,direction=ingress,protocol=icmp,v4-cidrs=[10.0.0.0/8],v4-cidrs=[192.168.0.0/16]" \
"--rule" "description=PODtoService,direction=ingress,from-port=1,to-port=65535,protocol=any,v4-cidrs=[10.97.0.0/16],v4-cidrs=[10.98.0.0/16]" \
"--rule" "description=Self,direction=ingress,from-port=1,to-port=65535,protocol=any,predefined=self_security_group" \
--network-id $NETWORK --description "k8s access" --folder-id $FOLDER --async
```

## SG для работы БД.
```
yc vpc sg create --name cam-db \
"--rule" "description=access all egress port,direction=egress,from-port=1,to-port=65535,protocol=any,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 22 port,direction=ingress,port=22,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 53 port,direction=ingress,port=53,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 80 port,direction=ingress,port=80,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=access 443 port,direction=ingress,port=443,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=PostgreSQL,direction=ingress,port=6432,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=ClickHouse,direction=ingress,port=8443,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=ClickHouse,direction=ingress,port=9440,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=ClickHouse,direction=ingress,port=9000,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=ClickHouse,direction=ingress,port=8123,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=YDB,direction=ingress,port=2135,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=MySQL,direction=ingress,port=3306,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=MongoDB,direction=ingress,port=27017,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=MongoDB,direction=ingress,port=27018,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Redis,direction=ingress,port=6379,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Redis,direction=ingress,port=6380,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Redis,direction=ingress,port=26379,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Kafka,direction=ingress,port=9091,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Kafka,direction=ingress,port=9092,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Elasticsearch,direction=ingress,port=9200,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=YARN,direction=ingress,port=8188,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=YARN,direction=ingress,port=8088,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Zeppelin,direction=ingress,port=8890,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Spark,direction=ingress,port=18080,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Oozie,direction=ingress,port=11000,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=MapReduce,direction=ingress,port=19888,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Livy,direction=ingress,port=8998,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Hive,direction=ingress,port=10002,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=Hadoop,direction=ingress,port=9870,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=HBase,direction=ingress,port=16010,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
"--rule" "description=HBase,direction=ingress,port=8085,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
--network-id $NETWORK --description "MDB access" --folder-id $FOLDER --async

# PostgreSQL
# ClickHouse
# YDB
# MySQL
# MongoDB
# Redis
# Kafka
# Elasticsearch
# Dataproc
```
## bash
```
export SG=$(yc vpc sg get k8s-sg --folder-name cameda-practicum --format=json | jq -r '.id')
export SG=$(yc vpc sg get cam-db --folder-name cameda-practicum --format=json | jq -r '.id')
export SG=$(yc vpc sg get cam-all --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get cam-default --folder-name cameda-practicum --format=json | jq -r '.id')
```
