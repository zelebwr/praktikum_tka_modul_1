# TKA Modul 1

Question Source: [Cloud Computing Practicum Module 1](https://docs.google.com/document/d/1vnD6l0r7BPj8p0vqhMG14pTPug0mWu37NKeGqQIT6w8/edit?tab=t.0)

Group Members:

1. Adiwidya @Riverzn
2. Prabaswara @zostradamus
3. Zelig @zelebwr

# Soal 1

## 📋 Deskripsi Tugas

> *"Markas NERV baru saja mengalami serangan Angel yang merusak sebagian infrastruktur server. Commander Ikari memerintahkan seluruh divisi teknis untuk segera membangun ulang sistem web operasional EVA."*

Tugas ini mensimulasikan pembangunan ulang infrastruktur web menggunakan Docker, dengan dua versi sistem yang berjalan bersamaan:
- **v1** — Sistem ringan dan cepat berbasis `nginx:latest`
- **v2** — Sistem robust berbasis `ubuntu:latest` dengan instalasi nginx manual

---

## 📁 Struktur Proyek

```
eva-project/
├── v1/
│   ├── Dockerfile
│   └── index.html
└── v2/
    ├── Dockerfile
    └── index.html
```

---

## 🔢 Soal & Penyelesaian

### Soal 1 — Inisialisasi Struktur Proyek

**Deskripsi:** Membuat folder `eva-project` beserta subfolder `v1` dan `v2`.

**Perintah:**
```bash
mkdir -p eva-project/v1 eva-project/v2
```

**Verifikasi:**
```bash
find eva-project -type d
```

**Output:**
```
eva-project
eva-project/v1
eva-project/v2
```

---

### Soal 2 — Membuat File `index.html`

**Deskripsi:** Masing-masing folder memiliki `index.html` dengan konten berbeda.

**File `v1/index.html`:**
```html
<html>
<body>
<h1>EVANGELION STATUS SYSTEM</h1>
<p>Unit-02 Pilot: Asuka Langley Soryu</p>
<p>Version: v1 - Lightweight</p>
</body>
</html>
```

**File `v2/index.html`:**
```html
<html>
<body>
<h1>EVANGELION STATUS SYSTEM</h1>
<p>Unit-02 Pilot: Asuka Langley Soryu</p>
<p>Version: v2 - Full System</p>
</body>
</html>
```

**Perintah:**
```bash
cat > eva-project/v1/index.html << 'EOF'
<html>
<body>
<h1>EVANGELION STATUS SYSTEM</h1>
<p>Unit-02 Pilot: Asuka Langley Soryu</p>
<p>Version: v1 - Lightweight</p>
</body>
</html>
EOF

cat > eva-project/v2/index.html << 'EOF'
<html>
<body>
<h1>EVANGELION STATUS SYSTEM</h1>
<p>Unit-02 Pilot: Asuka Langley Soryu</p>
<p>Version: v2 - Full System</p>
</body>
</html>
EOF
```

---

### Soal 3 — Dockerfile v1 (nginx:latest)

**Deskripsi:** Membuat Dockerfile untuk v1 menggunakan base image `nginx:latest`, menyalin `index.html` ke direktori default nginx, dan expose port 80.

**File `v1/Dockerfile`:**
```dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

**Penjelasan:**

| Instruksi | Penjelasan |
|-----------|-----------|
| `FROM nginx:latest` | Base image nginx siap pakai |
| `COPY index.html /usr/share/nginx/html/index.html` | Salin file ke direktori default nginx |
| `EXPOSE 80` | Buka port 80 di dalam container |

---

### Soal 4 — Dockerfile v2 (ubuntu:latest + nginx manual)

**Deskripsi:** Membuat Dockerfile untuk v2 menggunakan base image `ubuntu:latest`, menginstall nginx secara manual, dan menjalankan nginx di foreground.

**File `v2/Dockerfile`:**
```dockerfile
FROM ubuntu:latest

RUN apt-get update && apt-get install -y nginx

COPY index.html /var/www/html/index.html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**Penjelasan:**

| Instruksi | Penjelasan |
|-----------|-----------|
| `FROM ubuntu:latest` | Base image sistem operasi Ubuntu penuh |
| `RUN apt-get update && apt-get install -y nginx` | Install nginx secara manual |
| `COPY index.html /var/www/html/index.html` | Salin file ke direktori web nginx Ubuntu |
| `EXPOSE 80` | Buka port 80 di dalam container |
| `CMD ["nginx", "-g", "daemon off;"]` | Jalankan nginx di foreground |

**Perbedaan v1 vs v2:**

| | v1 | v2 |
|---|---|---|
| Base Image | `nginx:latest` | `ubuntu:latest` |
| Nginx | Sudah built-in | Di-install manual via `apt-get` |
| Direktori HTML | `/usr/share/nginx/html/` | `/var/www/html/` |
| CMD | Tidak perlu | Wajib ditambahkan |

---

### Soal 5 — Build Docker Image

**Deskripsi:** Build kedua image dengan format nama `eva-web-[nomor_kelompok]:v1` dan `eva-web-[nomor_kelompok]:v2`.

**Perintah:**
```bash
cd eva-project

# Build image v1
docker build -t eva-web-08:v1 ./v1

# Build image v2
docker build -t eva-web-08:v2 ./v2
```

**Verifikasi:**
```bash
docker images | grep eva-web-08
```

**Output:**
```
eva-web-08   v1    xxxxxxxxxxxx   X minutes ago   XXX MB
eva-web-08   v2    xxxxxxxxxxxx   X minutes ago   XXX MB
```

---

### Soal 6 — Menjalankan Kedua Container Serentak

**Deskripsi:** Menjalankan kedua container dalam mode detached dengan nama dan port mapping yang ditentukan.

**Perintah:**
```bash
# Jalankan container v1 di port 8080
docker run -d --name eva-container-08-v1 -p 8080:80 eva-web-08:v1

# Jalankan container v2 di port 8081
docker run -d --name eva-container-08-v2 -p 8081:80 eva-web-08:v2
```

**Penjelasan opsi:**

| Opsi | Penjelasan |
|------|-----------|
| `-d` | Mode detached (berjalan di background) |
| `--name` | Memberi nama pada container |
| `-p 8080:80` | Port host diarahkan ke port container |

---

### Soal 7 — Verifikasi Container & Browser

**Deskripsi:** Menampilkan daftar container yang berjalan dan memverifikasi tampilan di browser.

**Perintah:**
```bash
docker ps
```

**Output:**
```
CONTAINER ID   IMAGE            PORTS                  NAMES
xxxxxxxxxxxx   eva-web-08:v2    0.0.0.0:8081->80/tcp   eva-container-08-v2
xxxxxxxxxxxx   eva-web-08:v1    0.0.0.0:8080->80/tcp   eva-container-08-v1
```

**Akses Browser:**
- http://localhost:8080 → menampilkan `Version: v1 - Lightweight`
- http://localhost:8081 → menampilkan `Version: v2 - Full System`

---

### Soal 8 — Docker Volume pada Container v1

**Deskripsi:** Menghubungkan folder `eva-project/v1` di host ke direktori web server nginx di dalam container v1 menggunakan Docker Volume.

**Perintah:**
```bash
# Hentikan dan hapus container v1
docker stop eva-container-08-v1
docker rm eva-container-08-v1

# Jalankan ulang v1 dengan volume (Linux/Mac)
docker run -d --name eva-container-08-v1 -p 8080:80 \
  -v $(pwd)/v1:/usr/share/nginx/html eva-web-08:v1

# Untuk Windows PowerShell
docker run -d --name eva-container-08-v1 -p 8080:80 `
  -v ${PWD}/v1:/usr/share/nginx/html eva-web-08:v1
```

**Verifikasi (v2 tetap berjalan):**
```bash
docker ps
```

**Penjelasan Volume:**

| Bagian | Penjelasan |
|--------|-----------|
| `-v $(pwd)/v1` | Folder v1 di host (komputer lokal) |
| `:/usr/share/nginx/html` | Di-mount ke direktori nginx di container |
| Efek | Perubahan file di host langsung terlihat di container |

---

### Soal 9 — Membuktikan Docker Volume Bekerja

**Deskripsi:** Mengubah isi `index.html` di host dan membuktikan perubahan langsung terlihat di browser tanpa rebuild image.

**Edit file di host:**
```bash
cat > eva-project/v1/index.html << 'EOF'
<html>
<body>
<h1>EVANGELION STATUS SYSTEM</h1>
<p>Unit-02 Pilot: Asuka Langley Soryu</p>
<p>Version: v1 - Lightweight</p>
<p>Status: Unit-02 Launch Successful</p>
</body>
</html>
EOF
```

**Verifikasi:**
```bash
cat eva-project/v1/index.html
```

**Hasil di Browser (http://localhost:8080):**
```
EVANGELION STATUS SYSTEM
Unit-02 Pilot: Asuka Langley Soryu
Version: v1 - Lightweight
Status: Unit-02 Launch Successful   ← muncul tanpa rebuild!
```

> ✅ Perubahan langsung terlihat tanpa perlu `docker build` ulang — inilah keunggulan Docker Volume.

---

### Soal 10 — Masuk ke Container v2 dengan `exec`

**Deskripsi:** Masuk ke dalam container v2, menampilkan isi `index.html`, dan memverifikasi instalasi nginx.

**Masuk ke container:**
```bash
docker exec -it eva-container-08-v2 bash
```

**Di dalam container — tampilkan index.html:**
```bash
cat /var/www/html/index.html
```

**Output:**
```html
<html>
<body>
<h1>EVANGELION STATUS SYSTEM</h1>
<p>Unit-02 Pilot: Asuka Langley Soryu</p>
<p>Version: v2 - Full System</p>
</body>
</html>
```

**Di dalam container — verifikasi nginx:**
```bash
nginx -v
```

**Output:**
```
nginx version: nginx/1.xx.x
```

**Keluar dari container:**
```bash
exit
```

---

## 📊 Ringkasan Pengerjaan

| Soal | Task | Tools |
|------|------|-------|
| 1 | Inisialisasi struktur folder | `mkdir` |
| 2 | Membuat file `index.html` v1 & v2 | `cat` |
| 3 | Dockerfile v1 (`nginx:latest`) | Dockerfile |
| 4 | Dockerfile v2 (`ubuntu:latest` + nginx manual) | Dockerfile |
| 5 | Build kedua Docker image | `docker build` |
| 6 | Menjalankan kedua container serentak | `docker run` |
| 7 | Verifikasi container & browser | `docker ps` |
| 8 | Docker Volume pada container v1 | `docker run -v` |
| 9 | Membuktikan volume bekerja tanpa rebuild | Edit file host |
| 10 | Masuk container v2 dengan exec | `docker exec` |

---

## 🛠️ Teknologi yang Digunakan

- **Docker Desktop**
- **nginx:latest** — Web server ringan berbasis Alpine
- **ubuntu:latest** — Sistem operasi penuh
- **Docker Volume** — Sinkronisasi file host ke container

---

## 👥 Informasi Kelompok

| | |
|---|---|
| **Kelompok** | 08 |
| **Mata Kuliah** | Komputasi Awan |
| **Semester** | 4 |
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
CREATE DATABASE modul1_B08_database;
USE modul1_B08_database;

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
