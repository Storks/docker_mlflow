Проект для запуска mlflow в докере с использвоанием базы данных и s3 хранилища через docker-compose.



# Подготовительные шаги
## В корне проекта создаем:
* (если еще не создано) файлы для переменных сред
  - .env (обязательно! должен быть в .gitignore)
  - .env.example (задается только структура оригинального .env, без самих значений, этот файл храниться контролируется гитом)
* Директорию Docker (для хранения докерфайлов, volume и т.п.)
* Файл docker-compose.yaml

# Развёртывание локального S3 minio в докер образе в связке с nginx
* Заносим в docker-compose.yaml данные о minio и nginx c network s3.
* Создаем файл ./Docker/nginx.conf (с данными)
* Добавляем в файл .env строки (аналогичные строки, но без самих значений добавляем в .env.example):
  - MINIO_ROOT_USER = admin
  - MINIO_ROOT_PASSWORD = miniotestpass
* Запускаем через docker-compose up -d --build
* Заходим через браузер http://127.0.0.1:9001 и авторизуемся (логин пароль как в переменных MINIO_ROOT_USER, MINIO_ROOT_PASSWORD)
* Через веб-интерфейс создаем бакет:
  - Buckets -> Create bucket -> mlflow 
  
* Через веб-интерфейс создаем сервисный аккаунт (по сути API Key):
  - Identity -> Service Accounts -> Create service account.
  - Обязательно сохраняем сгенерированный secret key (т.к. его больше нельзя будет увидеть)
  - Добавляем в файл .env наши полученные Key ID/Secret Key (аналогичные строки, но без самих значений добавляем в
.env.example):
    - AWS_ACCESS_KEY_ID = 
    - AWS_SECRET_ACCESS_KEY = 
# Развернуть MLflow, используя СУБД и хранилище артефактов на свой выбор. 
Вместо удаленного хоста можно использовать Docker контейнеры.

* Разворачиваем контейнеры под СУБД (postgres)
  - Добавляем в docker-compose.yaml данные по БД (postgress) и pgadmin
* Добавляем в файл .env строки (аналогичные строки, но без самих значений добавляем в .env.example)
  - POSTGRES_USER = dbuser
  - POSTGRES_PASSWORD = dbtestpass
  - POSTGRES_DB = mlflow
  - PGADMIN_DEFAULT_EMAIL = "admin@admin.com"
  - PGADMIN_DEFAULT_PASSWORD = pgtestpass
* Запускаем через docker-compose up -d --build
* Заходим через браузер http://127.0.0.1:5050 и авторизуемся (через PGADMIN_DEFAULT_EMAIL, PGADMIN_DEFAULT_PASSWORD)
* Добавляем новый сервер:
  - Add New Server -> General -> Name: mlflow_backend
  - Connection:
    - Hostname/address: postgres (в качестве hostname модно использовать как имя сервиса, т.е. db, так и имя контейнера, т.е. postgres)
    - Maintenance database: mlflow
    - Username: dbuser
    - Password: dbtestpass
    - Save password: Yes
  - Save (По итогу мы должны подключиться к нашей БД)
# Разворачиваем docker с mlflow
 * создаем директорию ./Docker/mlflow_image, в ней создаем Dockerfile.
 * Добавляем в docker-compose.yaml инфу по mlflow.
 * Запускаем через docker-compose up -d --build
 * Переходим через браузер на http://127.0.0.1:5000 и убеждаемся что ui доступен
