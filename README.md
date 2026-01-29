# 05-virt-04-docker-in-practice
## homework
### Задача 0
> Версия docker compose  
Docker Compose version v5.0.2  
  
> Результат выполнения команды docker-compose --version  
bash: /usr/local/bin/docker-compose: No such file or directory  

<p align="center">
  <img src="Задача 0.JPG" width="800">
</p>

### Задача 1  

> Fork репозитория  
  
<https://github.com/ufilin/shvirtd-example-python/tree/main>  
  
> Файл Dockerfile.python  
  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork# cat Dockerfile.python  
FROM python:3.12-slim  
WORKDIR /app  
COPY . .  
RUN apt-get update && apt-get install uvicorn -y  
RUN pip install --no-cache-dir -r requirements.txt  
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork#  
  
> Файл .dockerignore  
  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork# cat .dockerignore  
compose.yaml  
Dockerfile  
Dockerfile.python  
.dockerignore  
.env  
.git  
.gitignore  
/haproxy  
init.sql  
LICENSE  
main.py  
/nginx  
proxy.yaml  
README.md  
schema.pdf  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork#   
  
  
> Сборка образа:  
  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork# docker build -f Dockerfile.python -t fastapi .  
[+] Building 93.8s (11/11) FINISHED                                                                               docker:default  
 => [internal] load build definition from Dockerfile.python                                                                 0.0s  
 => => transferring dockerfile: 257B                                                                                        0.0s  
 => [internal] load metadata for docker.io/library/python:3.12-slim                                                         1.2s  
 => [auth] library/python:pull token for registry-1.docker.io                                                               0.0s  
 => [internal] load .dockerignore                                                                                           0.0s  
 => => transferring context: 192B                                                                                           0.0s  
 => [1/5] FROM docker.io/library/python:3.12-slim@sha256:5e2dbd4bbdd9c0e67412aea9463906f74a22c60f89eb7b5bbb7d45b66a2b68a6   0.0s  
 => [internal] load build context                                                                                           0.0s  
 => => transferring context: 37B                                                                                            0.0s  
 => CACHED [2/5] WORKDIR /app                                                                                               0.0s  
 => [3/5] COPY . .                                                                                                          0.0s  
 => [4/5] RUN apt-get update && apt-get install uvicorn -y                                                                 20.1s  
 => [5/5] RUN pip install --no-cache-dir -r requirements.txt                                                               67.7s   
 => exporting to image                                                                                                      4.6s   
 => => exporting layers                                                                                                     4.6s   
 => => writing image sha256:c0e0ae4047da35e463ea18761bdaf521fd507d3391ed0d3d2b44084b430e99dc                                0.0s   
 => => naming to docker.io/library/fastapi                                                                                  0.0s   
root@debian:/home/debian/05-virt-04-docker-in-practice/fork#   
  
> Проверка коректности создания образа и коректности отработки .dockerignore  
  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork# docker run --rm -ti --name test fastapi:latest /bin/bash  
root@369c596784d0:/app# ls -la  
total 12  
drwxr-xr-x 1 root root 4096 Jan 27 18:25 .  
drwxr-xr-x 1 root root 4096 Jan 27 18:27 ..  
-rw-r--r-- 1 root root   73 Jan 25 22:29 requirements.txt  
root@369c596784d0:/app#   
  
> docker history  
  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork# docker history fastapi:latest   
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT  
c0e0ae4047da   3 minutes ago   CMD ["uvicorn" "main:app" "--host" "0.0.0.0"…   0B        buildkit.dockerfile.v0  
<missing>      3 minutes ago   RUN /bin/sh -c pip install --no-cache-dir -r…   156MB     buildkit.dockerfile.v0  
<missing>      4 minutes ago   RUN /bin/sh -c apt-get update && apt-get ins…   59.9MB    buildkit.dockerfile.v0  
<missing>      5 minutes ago   COPY . . # buildkit                             73B       buildkit.dockerfile.v0  
<missing>      8 days ago      WORKDIR /app                                    0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     CMD ["python3"]                                 0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     RUN /bin/sh -c set -eux;  for src in idle3 p…   36B       buildkit.dockerfile.v0  
<missing>      2 weeks ago     RUN /bin/sh -c set -eux;   savedAptMark="$(a…   36.8MB    buildkit.dockerfile.v0  
<missing>      2 weeks ago     ENV PYTHON_SHA256=fb85a13414b028c49ba18bbd52…   0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     ENV PYTHON_VERSION=3.12.12                      0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     ENV GPG_KEY=7169605F62C751356D054A26A821E680…   0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     RUN /bin/sh -c set -eux;  apt-get update;  a…   3.81MB    buildkit.dockerfile.v0  
<missing>      2 weeks ago     ENV LANG=C.UTF-8                                0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     ENV PATH=/usr/local/bin:/usr/local/sbin:/usr…   0B        buildkit.dockerfile.v0  
<missing>      2 weeks ago     # debian.sh --arch 'amd64' out/ 'trixie' '@1…   78.6MB    debuerreotype 0.17  
root@debian:/home/debian/05-virt-04-docker-in-practice/fork#   

> запуск web приложения без docker  
  
(venv) root@debian:/home/debian/05-virt-04-docker-in-practice/fork# uvicorn main:app --host 0.0.0.0 --port 5000 --reload  
INFO: Will watch for changes in these directories: [’/home/debian/05-virt-04-docker-in-practice/fork’]  
INFO: Uvicorn running on http://0.0.0.0:5000 (Press CTRL+C to quit)  
INFO: Started reloader process [194872] using WatchFiles  
INFO: Started server process [194883]  
INFO: Waiting for application startup.  
Приложение запускается…  
Соединение с БД установлено и таблица ‘requests’ готова к работе.  
INFO: Application startup complete.  
INFO: 127.0.0.1:54162 - “GET / HTTP/1.1” 200 OK  
  
> в другом терминале:  
(venv) root@debian:/home/debian/05-virt-04-docker-in-practice/fork# curl http://0.0.0.0:8090/  
curl: (7) Failed to connect to 0.0.0.0 port 8090 after 0 ms: Couldn’t connect to server  
(venv) root@debian:/home/debian/05-virt-04-docker-in-practice/fork# curl http://0.0.0.0:5000/  
"TIME: 2026-01-28 02:25:04  

> Добавление управления названием таблицы через ENV переменную

--- 1. Конфигурация ---  
Считываем конфигурацию БД из переменных окружения  
db_host = os.environ.get('DB_HOST', '127.0.0.1')  
db_user = os.environ.get('DB_USER', 'app')  
db_password = os.environ.get('DB_PASSWORD', 'very_strong')  
db_name = os.environ.get('DB_NAME', 'example')  
db_table = os.environ.get('TABLE_NAME', 'requests') <--### Добавление переменной  
  
@asynccontextmanager  
async def lifespan(app: FastAPI):  
    # Код, который выполнится перед запуском приложения  
    print("Приложение запускается...")  
    try:  
        with get_db_connection() as db:  
            cursor = db.cursor()  
            create_table_query = f"""  
            CREATE TABLE IF NOT EXISTS {db_name}.{db_table} ( <--### Правка в коде  
                id INT AUTO_INCREMENT PRIMARY KEY,  
                request_date DATETIME,  
                request_ip VARCHAR(255)  
            )  
            """  
   
### Задача 2  

> Отчёт сканирования  
  
<https://github.com/ufilin/05-virt-04-docker-in-practice/blob/main/vulnerabilities.csv>  
  
### Задача 3

> Результат
  
<p align="center">
  <img src="Задача 3.PNG" width="1000">
</p>

### Задача 4  
  
> Результат  
  
<p align="center">
  <img src="Задача 4.PNG" width="1000">
</p>

  
### Задача 5  

> 
  
### Задача 6  
  
> Загружаем  образый на ПК и запускаем dive  
  
<p align="center">
  <img src="Задача 6(download).png" width="1000">
</p>
  
> Запускаем dive и находим идентификатор нужного нам слоя  
  
<p align="center">
  <img src="Задача 6(dive).png" width="1000">
</p>
  
> Сохраняем образ в архиве  
  
<p align="center">
  <img src="Задача 6(docker save).png" width="1000">
</p>
  
> Разархивируем и находим слой с нужным идентификатором  
  
<p align="center">
  <img src="Задача 6(bin file).png" width="1000">
</p>
  
### Задача 6.1  
  
> Использование docker cp для копирования слоя  
  
<p align="center">
  <img src="Задача 6.1.png" width="1000">
</p>
  
### Задача 6.2  
  
### Задача 7  

  
