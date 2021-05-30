# Lesson 4

Имя инстанса: psql-lesson4  
project: otus-psql

Ставим docker **не** рекомендуемым способом
```bash
$ curl https://get.docker.com/ | bash
```

## Prepare
В инстансе идем в директорию `/home/ubuntu/lesson4`. **Важно перейти в эту директорию так там хранится пароль от postgres(файл: .env)**  
*Свитчимся в рута* `sudo su`

Создаем сеть для наших контейнеров
```bash
$ docker network create pg-net
```

### 1. Запуск сервера psql
```bash
$ docker run -d \
  --name psql_server \
  -v /$(pwd)/init_scripts:/docker-entrypoint-initdb.d/ \
  -v /var/lib/postgres:/var/lib/postgresql/data \
  --env-file $(pwd)/.env \
  -p 5432:5432 \
  --network pg-net \
  postgres:13
```
* Мы запустили оф контейнер postgresql, 
* пароль от пользователя postgres находится в файле .env
* Замонтировали `/var/lib/postgres` - это **data_dir**
* Замонтировали `/docker-entrypoint-initdb.d/` - с скриптами инициализации psql
* Пробросили порт 5432 

### 2. Запуск клиента psql
```bash
$ dokcer run -ti \
  --name psql_client \
  --network pg-net \
  ubuntu:20.04 \
  /bin/bash
```

Устанавливаем psql клиент внутри контейнера
```sh
apt-get update
apt-get install postgresql-client
```

Конектимся к инстансу
```bash
psql -h psql_server -U postgres -p 5432
```

Так как имя контейнера это DNS внутри docker network мы конектимся по имени `psql_server`  
Можно и по IP но для этого надо получить IP контейнера
```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' psql_server
OR
docker inspect psql_server | grep "IPAddress"
```

### 3. конект с внешнего IP
Было добавленно firewall правило в GCE `allow-psql` которое разрешает конект с любых IP на порт `5432`
