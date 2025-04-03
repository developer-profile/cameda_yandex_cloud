# Мониторинг кластера с помощью Prometheus и Grafana

## Описание.
Рассмторим процесс установки Prometheus и Grafana в кластер Kubernetes и настроим экспорт метрик из Prometheus в Grafana.

## Установка.
### Установка Prometheus.
```
helm upgrade --install prometheus prometheus \
--repo https://prometheus-community.github.io/helm-charts \
--namespace monitoring --create-namespace \
--debug
```

Get the Alertmanager URL by running these commands in the same shell:
```
  export POD_NAME=$(kubectl get pods --namespace monitoring -l "app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace monitoring port-forward $POD_NAME 9093
```

The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
prometheus-prometheus-pushgateway.monitoring.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
``` 
 export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus-pushgateway,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace monitoring port-forward $POD_NAME 9091
```
-------------------------------------------------------------------------------------------------------------------------

### Установка Trickster - кеширующего прокси.
Он ускоряет чтение из БД Prometheus.

Создаём файл trickster.yaml с содержимым.
```
frontend:
  listenAddress: ""
  tlsListenAddress: ""
  tlsListenPort: ""
  connectionsLimit: "0"
origins:
  - name: default
    originType: prometheus
    originURL: http://prometheus-server:80
profiler:
  enabled: false
  port: 6060
prometheusScrape: false
prometheus:
  serviceMonitor:
    enabled: false
    interval: 30s
    labels: {}
replicaCount: 1
image:
  repository: tricksterproxy/trickster
  tag: "1.1"
  pullPolicy: IfNotPresent
service:
  annotations: {}
  labels: {}
  clusterIP: ""
  externalIPs: []
  loadBalancerIP: ""
  loadBalancerSourceRanges: []
  metricsPort: 8481
  servicePort: 8480
  type: ClusterIP
ingress:
  enabled: false
  annotations: {}
  extraLabels: {}
  hosts: []
  tls: []
volumes:
  persistent:
    type: "persistentVolume"
    enabled: false
    mountPath: "/tmp/trickster"
    accessModes:
      - ReadWriteOnce
    annotations: {}
    existingClaim: ""
    size: 15Gi
  generic:
    type: "generic"
    enabled: true
    mountPath: "/tmp/trickster"
podAnnotations: {}
resources: {}
securityContext: {}
```

```
helm upgrade --install trickster trickster \
--repo https://helm.tricksterproxy.io \
--namespace monitoring --create-namespace \
-f trickster.yaml \
--debug
```

Get the trickster URL by running these commands in the same shell:
```  
export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=trickster,component=trickster" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace monitoring port-forward $POD_NAME 9090
```
-------------------------------------------------------------------------------------------------------------------------

### Установка Grafana.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: grafana-pvc
  namespace: monitoring
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: grafana
  name: grafana
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      securityContext:
        fsGroup: 472
        supplementalGroups:
          - 0
      containers:
        - name: grafana
          image: grafana/grafana:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 3000
              name: http-grafana
              protocol: TCP
          readinessProbe:
            failureThreshold: 3
            httpGet:
              path: /robots.txt
              port: 3000
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 30
            successThreshold: 1
            timeoutSeconds: 2
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 30
            periodSeconds: 10
            successThreshold: 1
            tcpSocket:
              port: 3000
            timeoutSeconds: 1
          resources:
            requests:
              cpu: 250m
              memory: 750Mi
          volumeMounts:
            - mountPath: /var/lib/grafana
              name: grafana-pv
      volumes:
        - name: grafana-pv
          persistentVolumeClaim:
            claimName: grafana-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
spec:
  ports:
    - port: 3000
      protocol: TCP
      targetPort: http-grafana
  selector:
    app: grafana
  sessionAffinity: None
  type: LoadBalancer
```
Подключимся к Grafana.
```
export GRAFANA_IP=$(kubectl get service/grafana -n monitoring -o jsonpath='{.status.loadBalancer.ingress[0].ip}') && \
export GRAFANA_PORT=$(kubectl get service/grafana -n monitoring -o jsonpath='{.spec.ports[0].port}') && \
echo http://$GRAFANA_IP:$GRAFANA_PORT

Login/pass: admin/admin
```

## Экспортируем в Grafana дашборд.
Connections->Add new connections->Add new datasource

Выбираем
Name — Prometheus.
URL — http://trickster:8480

И импортируем дашборд с id 8588.
