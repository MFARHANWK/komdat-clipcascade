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

### Pembahasan
## Analisis ClipCascade dan Perbandingannya dengan KDE Connect

ClipCascade merupakan aplikasi *clipboard synchronization* yang memungkinkan pengguna menyinkronkan isi clipboard secara otomatis antar perangkat, seperti Windows, macOS, Linux, dan Android. Aplikasi ini ditulis menggunakan bahasa pemrograman "Java" dan dapat dijalankan menggunakan "Docker" untuk mempermudah proses instalasi serta pengelolaan server. ClipCascade mendukung mekanisme komunikasi Peer-to-Server (P2S) dan Peer-to-Peer (P2P), serta menyediakan fitur enkripsi end-to-end untuk menjaga keamanan data. Beberapa kelebihan yang dimiliki ClipCascade antara lain:

- Mendukung sinkronisasi clipboard secara otomatis antar perangkat tanpa perlu mengirim data secara manual.
- Mendukung berbagai sistem operasi, termasuk Windows, macOS, Linux, dan Android.
- Menggunakan `Docker` untuk mempermudah proses instalasi, deployment, dan pengelolaan server.
- Memungkinkan penyimpanan data pengguna secara persisten melalui konfigurasi volume Docker.
- Menyediakan konfigurasi ukuran maksimum pesan dan mode komunikasi melalui variabel lingkungan (environment variables).
- Menggunakan lisensi `GPL-3.0`, sehingga kode sumbernya dapat dipelajari dan dikembangkan sesuai ketentuan lisensi tersebut.

Meskipun memiliki berbagai kelebihan, ClipCascade juga memiliki beberapa kekurangan, di antaranya:

- Membutuhkan koneksi jaringan agar sinkronisasi antar perangkat dapat berlangsung.
- Instalasi dan konfigurasi server memerlukan pemahaman dasar mengenai Docker, port jaringan, dan alamat server.
- Pada mode P2S, proses sinkronisasi bergantung pada ketersediaan server.
- Akses dari jaringan yang berbeda memerlukan konfigurasi jaringan tambahan agar server dapat dijangkau.
- Pengguna perlu memperhatikan keamanan akun, konfigurasi server, dan perlindungan data clipboard yang berpotensi memuat informasi sensitif.

Jika dibandingkan dengan aplikasi sejenis, yaitu KDE Connect, keduanya memiliki fungsi berbagi clipboard antar perangkat, tetapi menawarkan pendekatan yang berbeda. Berikut beberapa perbandingan antara ClipCascade dan KDE Connect:

- **ClipCascade** memungkinkan pengguna menjalankan server sendiri menggunakan Docker, sedangkan **KDE Connect** berfokus pada integrasi langsung antar perangkat dan umumnya digunakan melalui jaringan lokal.
- ClipCascade dapat digunakan sebagai sarana mempelajari arsitektur client-server dan konfigurasi server, sedangkan **KDE Connect** lebih menekankan integrasi berbagai fitur antar perangkat, seperti berbagi file, notifikasi, dan clipboard.
- ClipCascade mendukung konfigurasi mode P2S dan P2P, sedangkan KDE Connect mengandalkan komunikasi antar perangkat yang telah dipasangkan tanpa memerlukan server clipboard khusus.
- ClipCascade memberikan keleluasaan dalam mengelola server dan konfigurasi deployment, tetapi pengguna perlu mengelola sendiri ketersediaan serta keamanan servernya. Sementara itu, KDE Connect lebih praktis untuk penggunaan sehari-hari karena tidak mengharuskan pengguna menyiapkan server tersendiri.
- ClipCascade cocok digunakan untuk mempelajari deployment aplikasi berbasis Docker dan komunikasi data melalui server, sedangkan KDE Connect lebih cocok untuk mempelajari komunikasi antar perangkat dan integrasi layanan dalam jaringan lokal.

Dalam penerapannya pada mata kuliah Komunikasi Data dan Jaringan Komputer, ClipCascade dapat digunakan sebagai media pembelajaran untuk memahami komunikasi client-server, konfigurasi port, pertukaran data, dan pengujian konektivitas jaringan. Dengan menjalankan server menggunakan Docker dan menghubungkan klien Windows serta Android, mahasiswa dapat menguji proses sinkronisasi data dan mengamati pengaruh koneksi jaringan terhadap keberhasilan komunikasi. Aplikasi ini juga dapat dibandingkan dengan KDE Connect melalui pengujian waktu sinkronisasi, keberhasilan pengiriman data, penggunaan sumber daya, serta kemudahan konfigurasi.

Dengan demikian, ClipCascade tidak hanya berfungsi sebagai aplikasi berbagi clipboard, tetapi juga dapat dimanfaatkan sebagai sarana praktik untuk memahami penerapan komunikasi data, pengelolaan server, deployment berbasis container, dan keamanan pertukaran data dalam jaringan komputer.