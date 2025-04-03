# Установка Node Local DNS в Managed Kubernetes

## Поехали!
```
export HELM_EXPERIMENTAL_OCI=1 &&\
helm pull oci://cr.yandex/yc-marketplace/k8s.gcr.io/node-local-dns/chart \
  --version 1.5.1 \
  --untar && \
KUBE_DNS_IP="$(kubectl get svc kube-dns -n kube-system -o jsonpath={.spec.clusterIP})" && \
helm upgrade --install node-local-dns chart \
  --namespace kube-system \
  --debug \
  --set config.cilium=true \
  --set config.clusterIp=$KUBE_DNS_IP
```
