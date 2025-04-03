# Установка CSI для S3 в Managed Kubernetes

## Описание.
Поддерживает RWX для mk8s. Монтирует бакет в контейнер пода. В качестве драйвера используется geesefs.

## Поехали!
```
export BUCKET=cam-test
```
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/csi-s3/csi-s3 \
  --version 0.42.1 \
  --untar && \
helm install \
  --namespace default \
  --set secret.accessKey=YCAJE...M \
  --set secret.secretKey=YCMF9xLu6-fV \
  --set storageClass.singleBucket=$BUCKET \
  --set storageClass.mountOptions="--memory-limit=1000 --dir-mode=0777 --file-mode=0666 --debug_s3 --debug_fuse --log-file /var/log/s3.txt --no-systemd" \
  --set storageClass.reclaimPolicy=Retain \ 
  csi-s3 ./csi-s3/
```

## Пример PVC + Deployment.
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: csi-s3-pvc-dynamic
  namespace: default
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
  storageClassName: csi-s3
EOF
```

```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cam-nginx-s3
  labels: 
    app: nginx-test-s3
    environment: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-alpine
  strategy: 
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: nginx-alpine
    spec:
      containers:
      - name: nginx-alpine
        image: nginx:alpine
        imagePullPolicy: IfNotPresent
        ports: 
        - name: http
          containerPort: 80
        resources: 
          requests: 
            cpu: 30m
            memory: 20Mi 
          limits:  
            memory: 40Mi
        volumeMounts: 
        - mountPath: /mnt/s3 
          name: cameda-volume
      restartPolicy: Always
      volumes: 
      - name: cameda-volume 
        persistentVolumeClaim: 
          claimName: csi-s3-pvc-dynamic
EOF
```

## Лог.
Лог /var/log/s3.txt лежит на поде агенте csi-s3, который создаётся демонсетом. Данный лог необходим для дебага проблем с csi-s3. 
