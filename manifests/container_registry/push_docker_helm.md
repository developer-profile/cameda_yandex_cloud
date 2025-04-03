# Примеры пуша в CR docker image и helm chart

## Docker image.
```
Регистрация в реджистри: yc container registry configure-docker
Собираем образ: docker build -t cr.yandex/registry_id/<image_name>:<tag> -f Dockerfile .
Пушим образ в реджистри: docker push cr.yandex/registry_id/<image_name>:<tag>
```

## Helm chart
```
Регистрация в реджистри: yc container registry configure-docker

```
