# Установка Filebeat OSS в Managed Kubernetes

## Описание.
Агент выгрузки логов в Opensearch.

## Поехали!

1. В кластере Opensearch выполняем запрос.
```
wget "https://storage.yandexcloud.net/cloud-certs/CA.pem" && \
curl \
--user admin --cacert CA.pem \
--request PUT https://rc1d-p8f9lrc26irvhdce.mdb.yandexcloud.net:9200/_cluster/settings \
--header "Content-Type: application/json" \
--data \
'{
"persistent": {
  "compatibility": {
    "override_main_response_version": true
  }
}
}'
```

2. Проверим, что логи попадают в кластер Opensearch.
```
mkdir -p ~/.opensearch && \
wget "https://storage.yandexcloud.net/cloud-certs/CA.pem" \
   --output-document ~/.opensearch/root.crt && \
chmod 0600 ~/.opensearch/root.crt

curl \
    --user admin \
    --cacert ~/.opensearch/root.crt \
    --request GET 'https://c-c9qp85hci959a71ge762.rw.mdb.yandexcloud.net:9200/'
```

3. Ставим FilebeatOSS для выгрузки логов.
```
helm pull oci://cr.yandex/yc-marketplace/yandex-cloud/filebeat-oss/chart/filebeat-oss \
  --version 7.12.1-1 \
  --untar && \
helm install \
  --namespace filebeat \
  --create-namespace \
  --set app.url='https://rc1d-p8f9lrc26irvhdce.mdb.yandexcloud.net:9200' \
  --set app.username='admin' \
  --set app.password='P@$$w0rd' \
  filebeatoss ./filebeat-oss/
```
