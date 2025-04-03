# Lockbox

## Переменные.
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export KMS=$(yc kms symmetric-key get k8s-key --folder-name cameda-practicum --format json | jq -r ".id")
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
```

## Создадим lockbox и поместим в секретницу публичный ключ ssh.
```.
yc lockbox secret create \
  --folder-id $FOLDER \
  --name lockbox-secret \
  --description "k8s+lockbox" \
  --labels prod=lb-k8s \
  --kms-key-id $KMS \
  --payload "[{'key': 'ssh', 'text_value': 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDI98mJDBN9cnp6HOdBYTQILeAhUSDvDfoqA9iLmVPDyPLFRWs7tE4BjCAcFD6a3M50QIboCaohfa7h+PWksYibab7I3QHOR7y9pCW8FGonGRw2ACvt906qlaWHFj7jWOxuihFoiRROKqLCW5YE/Yc4XFIvW1gu3JQdvQ1wemWvujsI8EHE6PI1pEg7/41y6kn3IhNHIr8WRLe4dPyPGjwc4LpBCcaRSJiX4YjVXynSIHNk365UrL+nGv8ix7bW5FNCgGqSgfUTVCfMYLzQ/gYHPVQrcIvCeHjkwluH8Z3gXeN3OliejBjpLi+IWIzd9K6UADSUNU8oL+9941tDidp8APoe7RbB4h3bY6k8Bhy0yxohgQS2OWSYd1mjeEx8Ba5wzJKqfpUgmcPdrBJnBwLgLMFQyEfYG6vTPkYWAKEvkkJ6ZiA4tdoQvCb+B0xJV/ivHyLtoi3LFE59mbQFDUy8O51vX9JjBDLwzyTEeslWp7uOP66Ti5Q5ucNXbs5yXTU= cameda@cameda-osx'}]" \
  --deletion-protection \
  --async
```

## bash
```
export Lockbox=$(yc lockbox secret get lockbox-secret --folder-name cameda-practicum --format json | jq -r ".id")
```

## Добавим права на хранилище для SA.
```
yc lockbox secret add-access-binding $Lockbox \
  --service-account-id $SA \
  --role lockbox.admin,lockbox.payloadViewer \
  --async
```
