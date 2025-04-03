# KMS

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Create KMS for K8s and S3.
```
yc kms symmetric-key create \
  --folder-id $FOLDER \
  --name k8s-key \
  --description "k8s symmetric key" \
  --labels cam=k8s \
  --default-algorithm aes-256 \
  --deletion-protection \
  --rotation-period 24h \
  --async
```

## bash
```
export KMS=$(yc kms symmetric-key get k8s-key --folder-name cameda-practicum --format json | jq -r ".id")
```
