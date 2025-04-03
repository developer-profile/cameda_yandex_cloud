# Container Registry

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
```

## Create CR.
```
yc container registry create \
   --folder-id $FOLDER \
   --name cam-registry \
   --labels prod=cr \
   --async
```

## Info about CR.
```
yc container registry list
yc container registry get cam-registry
yc container registry list-access-bindings cam-registry
yc container registry list-ip-permissions cam-registry
```

## BASH
```
export REGISTRY=$(yc container registry get cam-registry --folder-name cameda-practicum --format=json | jq -r ".id")
```

## Auth in Docker
```
yc container registry configure-docker
```

## Example
-------------------------------------------------------------------
```
mkdir -p ~/docker/nginx && cd ~/docker/nginx
vi nginx.dockerfile

FROM nginx:latest
LABEL maintainer="cameda4@yandex.ru"
RUN echo "Hello, world!"
COPY index.html /usr/share/nginx/html/index.html

docker build . -f nginx.dockerfile -t cr.yandex/$REGISTRY/nginx:a1
docker push cr.yandex/${REGISTRY}/nginx:a1
```
---------------------------------------------------------------------
```
yc container image list
```
