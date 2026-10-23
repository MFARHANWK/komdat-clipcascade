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
    ports:
      - "8080:8080"
    restart: unless-stopped
    volumes:
      - ./cc_users:/database
    environment:
      CC_MAX_MESSAGE_SIZE_IN_MiB: "1" # Maksimum ukuran pesan dalam MiB
      CC_P2P_ENABLED: "false" # matikan mode P2P

```

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

### 2.6 (Opsional) Memastikan container berjalan via localhost

Buka browser, kemudian ketikkan https://localhost:8080

## 3 Konfigurasi server

Berisi

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
Username: Admin
Password: Admin123
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
   ![]()
2. Masukan konfigurasi berikut:

```
Username: Admin
Password: Admin123
Server URL: http://localhost:8080
```

3. Klik Login, jika login berhasil maka akan muncul tampilan berikut:
   ![]()
4. Klik Start, maka client akan terhubung ke server.
