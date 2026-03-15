# TKA Modul 1

Question Source: [Cloud Computing Practicum Module 1](https://docs.google.com/document/d/1vnD6l0r7BPj8p0vqhMG14pTPug0mWu37NKeGqQIT6w8/edit?tab=t.0)

Group Members:

1. Adiwidya @Riverzn
2. Prabaswara @zostradamus
3. Zelig @zelebwr

# Soal 1

# Soal 2
Nomer 2 ini menyiapkan lingkungan database server berbasis container Docker yang berjalan di atas sistem operasi Debian 10. Server database ini digunakan oleh tim riset NERV untuk menyimpan dan mengelola data para pilot Evangelion.

| Komponen | Detail |
|---|---|
| Base OS | Debian 10 (Buster) |
| Hostname | `database-shinji` |
| Database | MariaDB Server 10.3 |
| CPU Limit | 2 vCPU |
| RAM Limit | 2048 MB |
| Volume Mount | `./data` → `/var/data/db` |

## Struktur Folder
```
nerv-database/
├── Dockerfile           # Konfigurasi image container
├── docker-compose.yml   # Konfigurasi resource, volume, dan jaringan
└── data/                # Direktori host yang di-mount ke /var/data/db di container
```

## Langkah Pengerjaan
### Prerequisites
- Windows 10/11
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) sudah terinstall dan **running**
- WSL2 sudah aktif

### 1. Buat Struktur Folder
```powershell
cd $HOME\Documents
mkdir nerv-database
cd nerv-database
mkdir data
```

### 2. Buat `Dockerfile`
```dockerfile
FROM debian:10

ENV HOSTNAME=database-shinji

RUN sed -i 's|http://deb.debian.org/debian|http://archive.debian.org/debian|g' /etc/apt/sources.list && \
    sed -i 's|http://security.debian.org|http://archive.debian.org/debian-security|g' /etc/apt/sources.list && \
    sed -i '/buster-updates/d' /etc/apt/sources.list && \
    apt-get update && \
    apt-get install -y mariadb-server && \
    apt-get clean

RUN mkdir -p /var/data/db

EXPOSE 3306

CMD mysqld_safe
```

### 3. Buat `docker-compose.yml`
```yaml
services:
  database-shinji:
    build: .
    container_name: database-shinji
    hostname: database-shinji
    volumes:
      - ./data:/var/data/db
    deploy:
      resources:
        limits:
          cpus: '2.0'
          memory: 2048M
    ports:
      - "3306:3306"
    restart: unless-stopped
```

### 4. Build & Jalankan Container
```powershell
docker-compose build
docker-compose up -d
```

### 5. Verifikasi Container
```powershell
docker ps
docker exec -it database-shinji bash
```

Di dalam container:
```bash
service mysql status
```
### 6. Buat Database & Tabel
```bash
mysql -u root
```
```sql
CREATE DATABASE modul1_[nomor_kelompok]_database;
USE modul1_[nomor_kelompok]_database;

CREATE TABLE pilot_data (
    NRP        VARCHAR(20)  NOT NULL PRIMARY KEY,
    Nama       VARCHAR(100) NOT NULL,
    Departemen VARCHAR(100) NOT NULL
);

INSERT INTO pilot_data (NRP, Nama, Departemen) VALUES
('5027231012', 'Adiwidya Budi Pratama', 'Teknologi Informasi'),
('5027231047', 'Jonathan Zelig Sutopo', 'Teknologi Informasi');
('5027231069', 'Prabaswara Febrian Winandika', 'Teknologi Informasi');

SELECT * FROM pilot_data;
```

# Soal 3

## 1. Create Project Directory

We can create the initial project directory easily with this command:

```bash
$ mkdir -r eva-project/app,logs && cd eva-project/ && touch Dockerfile && touch app/index.html
```

## 2. Create the Simple Landing Page

The [index.html](./soal_3/eva-project/app/index.html) can be filled with a simple HTML, such as:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <p>EVANGELION STATUS SYSTEM</p>
    <p>Unit-01 Ready for Launch</p>
  </body>
</html>
```

## 3. Initial Dockerfile Creation

Fill the [Dockerfile](./soal_3/eva-project/app) with this:

```dockerfile
FROM nginx:latest
COPY app/index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

## 4. Building the Docker Image

Run this command on docker CLI:

```bash
$ docker build -t eva-web-b08:v1 .
```

## 5. Run the Docker Image

Initially, to create and start a new container with a new docker image, you can run this:

```bash
$ docker run --name eva-container-b08 -p 8080:80  -v $(pwd)/app:/usr/share/nginx/html -v $(pwd)/logs:/var/log/nginx eva-web-b08:v1
```

After running that, we can check if it's successfully running by running:

```bash
$ docker ps
```

or

```bash
$ docker container ls
```

## 6. Make Sure the Container is Running

Go visit the [local web](http://localhost:8080) to check if the container is running alright.

Or just use this command:

```bash
$ curl http://localhost:8080
```

## 7. Make Sure the Volume is Mounted Successfully

To check if the volume is mounted successfully, we can test it by changing the content of the [index.html](./soal_3/eva-project/app/index.html) in the host's directory into:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <p>EVANGELION STATUS SYSTEM</p>
    <p>Unit-01 Launch Successful</p>
  </body>
</html>
```

If the volume was mounted successfully the [local web page](http://localhost:8080) should also be updated automatically updated.

Either just access the [local web page](http://localhost:8080) again or just use this command again:

```bash
$ curl http://localhost:8080
```

## 8. RCA Docker Internal

Here, in the practicum we're needed to:

1. Check the container's directory
2. Check the contents of the index.html through the container
3. Check the container's log in real-time

Below are the commands we can use to access the container's directory list the directory and to read files inside of it, by simulating a terminal that is connected to the container:

```bash
$ docker exec -it eva-container-b08 bash
$ ls -l
$ cd /usr/share/nginx/html/ && cat index.html
```

Usually we can use the command below to output the log of the container, using the command below:

```bash
$ docker logs -f eva-container-b08
```

But since the `/var/log/nginx` is already mounted to `eva-project/logs` that won't work, since usual logs are now not going to the container's Standar Ouptut stream.

So, what we can do is to check the log of the physical file live to check the log. We can do that by doing the commands below:

```bash
$ docker exec -it eva-container-b08 bash
$ cd /var/log/nginx/ && tail -f access.log
```

To check if the log file is actually working live, we can just try accessing the [local web page](http://localhost:8080) through our browser, or just use:

```bash
$ curl http://localhost:8080
```

## 9. Showing Docker Processes

For this step, our practicum wants us to:

1. Show a list of running containers
2. Show a list of available images
3. Show the container's resource usage statistics

To all of the above, we can use the commands below:

```bash
# 1. To show a list of running containers
$ docker ps
# or we can also use
$ docker container ls
# 2. To show a list of availabe images
$ docker images
# 3. To show the container's resource usage statistics
$ docker stats eva-container-b08
```

## 10. Docker Cleanup

After doing all of the above (from number 1-9), in the last step, we're asked to clean up all of that.

The cleanup consists of:

1. Stopping the running container
2. Removing the container
3. Removing the docker images

For all of those we can use these commands:

```bash
# To stop the running container
$ docker stop eva-container-b08
# To remove the container
$ docker rm eva-container-b08
# To remove the Docker Image
$ docker rmi eva-web-b08:v1
```
