# Panduan Instalasi Clipcascade di Ubuntu Server

## Daftar isi

1. a
2. b

## 1. Instalasi Docker

### 1.1 Set up docker apt repository

```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

### 1.2 Install Docker Packages

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 1.3 Cek apakah docker sudah berjalan

```
sudo systemctl status docker
```

Jika belum berjalan, maka jalankan menggunakan perintah berikut

```
sudo systemctl start docker
```

### 1.4 (Opsional) Verifikasi instalasi dengan menjalankan image hello-world

```
sudo docker run hello-world
```

Berikut tampilan yang dihasilkan
![Hasil hello-world image](/images/01.png)

## 2. Instalasi clipcascade via docker

### 2.1 Membuat direktori untuk menyimpan file konfigurasi container clipcascade

```
mkdir -p clipcascade
cd clipcascade
mkdir cc_users
```

direktori cc_users digunakan untuk menyimpan data pengguna

### 2.2 Konfigurasi docker-compose.yml

```
nano docker-compose.yml
```

Kemudian isikan konfigurasinya seperti berikut ini

```
services:
  clipcascade:
    image: sathvikrao/clipcascade:latest
    container_name: clipcascade
    restart: unless-stopped

    ports:
      - "127.0.0.1:18080:8080"

    volumes:
      - ./cc_users:/database                # Penyimpanan untuk data pengguna

    environment:
      CC_MAX_MESSAGE_SIZE_IN_MiB: "1"       # Maksimum ukuran pesan dalam MiB (Default: 1)
      CC_P2P_ENABLED: "false"               # Mematikan mode P2P (Default : false)
      CC_SIGNUP_ENABLED: "false"            # Mematikan fitur signup (Default : false)
      CC_PORT: "8080"                       # Set port aplikasi (Default : 8080)
```

Keterangan :

- `"127.0.0.1:18080:8080"` akan mengatur container clipcascade hanya bisa diakses oleh localhost (127.0.0.1) dengan port 18080 oleh server dan mengarahkan koneksi tersebut ke port 8080 di dalam container.

### 2.3 Validasi konfigurasi apakah sudah benar

```
sudo docker compose config
```

### 2.4 Jalankan container clipcascade

```
sudo docker compose up -d
```

### 2.5 Melihat log dari container clipcascade

```
sudo docker compose logs --tail=50
```

### 2.6 Tes akses langsung dari terminal server

```
curl -I http://127.0.0.1:18080/
```

Akan menghasilkan keluaran seperti berikut ini

![Hasil keluaran curl ke clipcascade](/images/02.png)

## 3 Konfigurasi server

**_Catatan:_** Server mhaidar.tech menggunakan reverse proxy agar bisa menjalankan beberapa aplikasi dan dapat diakses secara publik. Terdapat aplikasi nginx yang bertugas untuk mengarahkan koneksi yang masuk kepada aplikasi yang bersesuaian.

### 3.1 Membuat direktori untuk konfigurasi container nginx

```
mkdir -p nginx
cd nginx
```

### 3.2 Konfigurasi docker-compose.yml untuk nginx

Sebelum itu, kita harus membuat jaringan docker lalu memasukkan clipcascade dan nginx (akan dikonfigurasi pada docker-compose.yml) ke dalam jaringan ini. Fungsi dari jaringan docker ini adalah agar antar aplikasi bisa saling berkomunikasi.

```
docker network create homeserver
```

Kemudian tambahkan aplikasi clipcascade ke dalam jaringan `homeserver`

```
docker network connect homeserver clipcascade
```

Langkah selanjutnya adalah mengonfigurasi docker-compose.yml untuk nginx

```
nano docker-compoose.yml
```

Isikan file ``docker-compoose.yml` dengan isian berikut ini

```
services:
  nginx:
    image: nginx:alpine
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    restart: unless-stopped
    networks:
      - homeserver

networks:
  homeserver:
    external: true
```

Perhatikan bahwa nginx membutuhkan file `nginx.conf`` untuk di-mount ke `etc/nginx/nginx.conf``` pada container

### 3.3 Membuat nginx.conf

Membuat file `nginx.conf`

```
nano nginx.conf
```

Isikan file `nginx.conf` dengan isian berikut ini

```
events {}

http {
    server {
        listen 80;
        server_name _;

        location / {
            proxy_pass http://clipcascade:8080;

            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;

            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

            proxy_read_timeout 3600s;
            proxy_send_timeout 3600s;
        }
    }
}
```

`proxy_pass http://clipcascade:8080;` artinya segala koneksi yang mengakses `/` akan diteruskan oleh nginx ke container clipcascade dengan port 8080

### 3.5 Validasi konfigurasi dan jalankan container

```
docker compose config
```

Jika tidak ada error, jalankan container dengan mengetikkan

```
docker compose up -d
```

Kemudian periksa status keberhasilan

```
docker compose ps
```

**_Pastikan container nginx dalam status 'Up'_**

## 4. Konfigurasi clipcascade client

## 4.1 Konfigurasi clipcascade client untuk Windows

### 4.1.1 Unduh clipcascade client untuk Windows

Buka halaman GitHub berikut:
<https://github.com/Sathvik-Rao/ClipCascade/releases>

Kemudian pilih rilis terbaru lalu unduh ClipCascade.msi

### 4.1.2 Install clipcascade client

Setelah ClipCascade.msi berhasil diunduh, lakukan instalasi dengan langkah berikut:

1. Buka file ClipCascade.msi yang berhasil diunduh, berikut tampilan yang muncul:

   ![Tampilan file ClipCascaade.msi](/images/Clipcascade.msi.png)

2. Klik next hingga muncul menu Select Installation Folder seperti berikut:

   ![Menu Select Installation Folder](/images/SelectInstallationFolder.png)

   Lokasi folder clipcascade dapat disesuaikan dengan menambahkan path yang diinginkan atau klik browse.

3. Klik next, lalu install. Setelah itu proses instalasi akan dimulai.

4. Setelah instalasi selesai, klik finish.

   ![Finish](/images/Finish.png)

### 4.1.3 Hubungkan client ke server

1. Buka aplikasi clipcascade yang sudah diinstal, berikut tampilan yang akan muncul:

![Konfigurasi ClipCascade](/images/KonfigurasiClipcascade.png)

2. Masukan konfigurasi berikut:

```
Username: admin
Password: admin123
Server URL: http://localhost:8080
```

3. Klik Login, jika login berhasil maka akan muncul tampilan berikut:

   ![Login Success](/images/LoginSuccess.png)

## 4.2 Konfigurasi clipcascade client untuk Android

### 4.2.1 Unduh clipcascade client untuk Android

Buka halaman GitHub berikut:
<https://github.com/Sathvik-Rao/ClipCascade/releases>

Kemudian pilih rilis terbaru lalu unduh ClipCascade.apk

### 4.2.2 Install clipcascade client

Setelah ClipCascade.apk berhasil diunduh, lakukan instalasi dengan langkah berikut:

1. Buka file ClipCascade.apk yang berhasil diunduh
2. Ikuti langkah instalasi sampai aplikasi berhasil diunduh

\*Jika aplikasi tidak dapat di instal karena berasal dari sumber yang tidak dikenal, ubah pengaturan lalu izinkan instal aplikasi dari sumber tersebut.

### 4.2.3 Hubungkan client ke server

1. Buka aplikasi clipcascade yang sudah diinstal, berikut tampilan yang akan muncul:

   ![ClipCascade Android](/images/ClipCascadeAndroid.jpeg)

2. Masukan konfigurasi berikut:

```
Username: admin
Password: admin123
Server URL: http://localhost:8080
```

3. Klik Login, jika login berhasil maka akan muncul tampilan berikut:

   ![Konfigurasi ClipCascade](/images/LoginSuccessAndroid.jpeg)

4. Klik Start, maka client akan terhubung ke server.

![Start Android](/images/Start.jpeg)
