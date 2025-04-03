# Cloud Logging

## BASH
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Create Cloud Logging
```
yc logging group create \
  --folder-id $FOLDER \
  --name k8s \
  --description "k8s" \
  --retention-period=1h \
  --async
```
```
export LOG=$(yc log group get k8s --folder-name cameda-practicum --format json | jq -r ".id")
```

## Create Cloud Logging group for FluentBit log from mk8s.
```
yc logging group create \
  --folder-id $FOLDER \
  --name k8s-fluentbit \
  --description "mk8s+fluentbit logs" \
  --retention-period=10h \
  --async
```
```
export LOG-FLUENT=$(yc log group get k8s-fluentbit --folder-name cameda-practicum --format json | jq -r ".id")
```
