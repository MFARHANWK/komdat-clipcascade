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
