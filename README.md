<h1 align="center"><img src="https://github.com/user-attachments/assets/710bb1c3-0eda-48cf-819a-e066bde3a3ec" width="200" alt="ClipCascade"></h1>

| [Sekilas Tentang](#sekilas-tentang) | [Instalasi](#instalasi) | [Konfigurasi](#konfigurasi) | [Cara Pemakaian](#cara-pemakaian) | [Pembahasan](#pembahasan) | [Referensi](#referensi) |
| :---------------------------------: | :---------------------: | :-------------------------: | :-------------------------------: | :-----------------------: | :---------------------: |

# Sekilas Tentang

[`^ kembali ke atas ^`](#)

ClipCascade adalah open-source utility yang ringa secara otomatis menyinkronkan clipboard berbagai perangkat—tanpa perlu memasukkan data secara manual. Aplikasi ini memastikan proses berbagi yang lancar dengan end-to-end encryption, sehingga memberikan pengalaman penggunaan clipboard yang aman di seluruh workstation.

# Instalasi

[`^ kembali ke atas ^`](#)

#### Kebutuhan Sistem :

- Sistem Operasi: Linux (Ubuntu Server disarankan).
- Docker Engine & Docker Compose Plugin.
- Akses pengguna dengan hak akses `sudo`.
- Koneksi Internet untuk mengunduh paket dan _image_ Docker.

#### Proses Instalasi :

### 1. Instalasi Docker

1. **Set up repositori Docker Apt**

   ```bash
   # Menambahkan official GPG key milik Docker:
   sudo apt update
   sudo apt install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc

   # Menambahkan repositori ke Apt sources:
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

2. **Install paket-paket Docker**

   ```bash
   sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

3. **Cek apakah Docker sudah berjalan**

   ```bash
   sudo systemctl status docker
   ```

   Jika belum berjalan, jalankan menggunakan perintah berikut:

   ```bash
   sudo systemctl start docker
   ```

4. **_(Opsional)_ Verifikasi instalasi dengan menjalankan image `hello-world`**

   ```bash
   sudo docker run hello-world
   ```

   Berikut tampilan yang dihasilkan:

   ![Hasil hello-world image](/images/01.png)

### 2. Instalasi ClipCascade via Docker

1. **Membuat direktori untuk menyimpan file konfigurasi container ClipCascade**

   ```bash
   mkdir -p clipcascade
   cd clipcascade
   mkdir cc_users
   ```

   _Direktori `cc_users` digunakan untuk menyimpan database dan data pengguna._

2. **Konfigurasi `docker-compose.yml`**

   ```bash
   nano docker-compose.yml
   ```

   Kemudian isikan konfigurasinya seperti berikut:

   ```yaml
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
         CC_P2P_ENABLED: "false" # Matikan mode P2P
   ```

3. **Validasi konfigurasi apakah sudah benar**

   ```bash
   sudo docker compose config
   ```

4. **Jalankan container ClipCascade**

   ```bash
   sudo docker compose up -d
   ```

5. **Melihat log dari container ClipCascade**

   ```bash
   sudo docker compose logs --tail=50
   ```

6. **_(Opsional)_ Memastikan container berjalan via localhost**
   Buka browser, kemudian akses `http://localhost:8080` (atau `https://localhost:8080`).

# Konfigurasi

[`^ kembali ke atas ^`](#)

### 1. Konfigurasi Server

Pengaturan server ClipCascade dapat dikonfigurasi melalui _environment variable_ pada berkas `docker-compose.yml`:

- `CC_MAX_MESSAGE_SIZE_IN_MiB`: Menentukan batas maksimal ukuran pesan/konten clipboard yang dapat dikirim (contoh: `"1"` MiB).
- `CC_P2P_ENABLED`: Mengatur mode koneksi Peer-to-Peer (`"false"` untuk menonaktifkan P2P sehingga sinkronisasi melewati server).

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

### 2. Konfigurasi ClipCascade Client

#### 2.1 Konfigurasi ClipCascade Client untuk Windows

1. **Unduh ClipCascade Client untuk Windows**  
   Buka halaman GitHub Releases berikut:  
   <https://github.com/Sathvik-Rao/ClipCascade/releases>  
   Pilih rilis terbaru lalu unduh berkas `ClipCascade.msi`.

2. **Install ClipCascade Client**  
   Setelah `ClipCascade.msi` berhasil diunduh, lakukan instalasi dengan langkah berikut:
   - Buka file `ClipCascade.msi` yang berhasil diunduh, berikut tampilan yang muncul:

     ![Tampilan file ClipCascade.msi](/images/Clipcascade.msi.png)

   - Klik **Next** hingga muncul menu _Select Installation Folder_ seperti berikut:

     ![Menu Select Installation Folder](/images/SelectInstallationFolder.png)

     _Lokasi folder ClipCascade dapat disesuaikan dengan menambahkan path yang diinginkan atau klik tombol Browse._

   - Klik **Next**, lalu klik **Install**. Setelah itu proses instalasi akan dimulai.
   - Setelah instalasi selesai, klik **Finish**:

### 2.6 Tes akses langsung dari terminal server

```
curl -I http://127.0.0.1:18080/
```

Akan menghasilkan keluaran seperti berikut ini

![Hasil keluaran curl ke clipcascade](/images/02.png)
![Finish](/images/Finish.png)

3. **Hubungkan Client ke Server**
   - Buka aplikasi ClipCascade yang sudah diinstal, berikut tampilan yang akan muncul:

     ![Konfigurasi ClipCascade](/images/KonfigurasiClipcascade.png)

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

- Masukkan konfigurasi berikut:

  ```text
  Username: admin
  Password: admin123
  Server URL: http://localhost:8080
  ```

- Klik **Login**, jika login berhasil maka akan muncul tampilan konfirmasi sukses:

  ![Login Success](/images/LoginSuccess.png)

#### 2.2 Konfigurasi ClipCascade Client untuk Android

1. **Unduh ClipCascade Client untuk Android**  
   Buka halaman GitHub Releases berikut:  
   <https://github.com/Sathvik-Rao/ClipCascade/releases>  
   Pilih rilis terbaru lalu unduh berkas `ClipCascade.apk`.

2. **Install ClipCascade Client**  
   Setelah `ClipCascade.apk` berhasil diunduh, lakukan instalasi dengan langkah berikut:
   - Buka berkas `ClipCascade.apk` yang berhasil diunduh.
   - Ikuti langkah instalasi sampai aplikasi berhasil terpasang.
   - _\*Jika aplikasi tidak dapat diinstal karena berasal dari sumber yang tidak dikenal, ubah pengaturan perangkat lalu izinkan instal aplikasi dari sumber tersebut (Unknown Sources)._

3. **Hubungkan Client ke Server**
   - Buka aplikasi ClipCascade yang sudah diinstal pada Android:

     ![ClipCascade Android](/images/ClipCascadeAndroid.jpeg)

   - Masukkan parameter konfigurasi berikut:

     ```text
     Username: admin
     Password: admin123
     Server URL: http://localhost:8080
     ```

   - Klik **Login**, jika login berhasil maka akan muncul tampilan status:

     ![Konfigurasi ClipCascade](/images/LoginSuccessAndroid.jpeg)

   - Klik **Start**, maka client akan terhubung ke server dan sinkronisasi clipboard siap digunakan:

     ![Start Android](/images/Start.jpeg)

#### 2.3 Konfigurasi ClipCascade Client untuk Linux (ubuntu/debian based)

1. **Untuk `ClipCascade_Linux.tar.xz`** di https://github.com/Sathvik-Rao/ClipCascade/releases

2. **Ekstrak file tersebut dengan menjalankan perintah berikut ini**

```
tar -xf ClipCasced_Linux.tar.xz ClipCascade/
```

Nantinya hasil ekstrak akan diletakkan di folder `ClipCascade`

Kemudian ubah direktori aktif ke direktori `ClipCascade`

```
cd ClipCascade
```

**_Jika perintah tar tidak ditemukan, install terlebih dahulu utilitas tersebut_**

```
sudo apt update && sudo apt install xz-utils tar
```

3. **Membuat virtual environment python**

```
python3 -m venv .venv
```

Aktivasi virtual environment tersebut

```
source .venv/bin/activate
```

**Pastikan terdapat tulisan `(.venv)` pada terminal**

Lalu install seluruh library pendukung yang ada pada file `requirements.txt`

```
pip install -r requirements.txt
```

Kemudian lanjutkan dengan menginstall library `Tkinter`

```
sudo apt install python3-tk
```

4. **Menjalankan program ClipCascade**

Setelah semua kebutuhan sudah diinstall, sekarang jalankan program `main.py`

```
python3 main.py
```

Maka sebuah window akan muncul, terlihat seperti berikut ini

![Window ClipCascade pada linux](/images/03.png)

5. **Konfigurasi ClipCascade Client**

Masukkan parameter konfigurasi berikut:

```
Username: admin
Password: admin123
Server URL: http://localhost:8080
```

Kemudian klik **Login**

# Cara Pemakaian

[`^ kembali ke atas ^`](#)

1. **Halaman login aplikasi desktop**  
   Pengguna memasukkan username, password, dan URL server ClipCascade yang akan dihubungkan. URL server menunjukkan alamat layanan ClipCascade yang digunakan oleh aplikasi untuk berkomunikasi dan menyinkronkan clipboard.

   ![Halaman login aplikasi desktop](/images/Gambar1.jpeg)

2. **Pemberitahuan login berhasil**  
   Setelah akses diterima, aplikasi menampilkan pesan bahwa proses login berhasil. ClipCascade kemudian berjalan di latar belakang melalui area notifikasi/taskbar atau menu bar, sehingga pengguna dapat mengakses fungsinya tanpa harus membiarkan jendela aplikasi tetap terbuka.

   ![Pemberitahuan login berhasil](/images/Gambar2.jpeg)

3. **Peringatan ketika aplikasi sudah berjalan**  
   Jika pengguna mencoba membuka atau menjalankan ClipCascade kembali saat sesi sebelumnya masih aktif, aplikasi menampilkan peringatan bahwa ClipCascade sudah berjalan. Pemberitahuan ini mencegah aplikasi dijalankan berulang kali secara tidak sengaja.

   ![Peringatan ketika aplikasi sudah berjalan](/images/Gambar3.jpeg)

4. **Halaman login pada website ClipCascade**  
   Kemudian pengguna membuka website ClipCascade pada alamat server yang sama lalu masuk menggunakan akun yang sama saat menyinkronkan clipboard.

   ![Halaman login pada website ClipCascade](/images/Gambar4.jpeg)

5. **Halaman utama dan koneksi WebSocket**  
   Pada halaman utama, pengguna menekan tombol **Connect** untuk menyambungkan browser ke layanan WebSocket ClipCascade. Jika koneksi berhasil, muncul status “Connected to WebSocket server (P2S)”. Status ini menunjukkan bahwa halaman tersebut telah terhubung ke server untuk menerima atau meneruskan data clipboard.

   ![Halaman utama dan koneksi WebSocket](/images/Gambar5.jpeg)

6. **Halaman ClipCascade Advance**  
   Halaman Advance menyediakan informasi administratif dan pemantauan, seperti detail pengguna, statistik WebSocket, catatan perlindungan brute-force, waktu server, serta variabel lingkungan. Bagian ini ditujukan untuk pemeriksaan dan pengelolaan server, sehingga pengguna biasa tidak perlu mengubah pengaturannya saat melakukan sinkronisasi clipboard.

   ![Halaman ClipCascade Advance](/images/Gambar6.jpeg)

7. **Mengganti username**  
   Menu **Change Username** pada bagian header digunakan untuk mengubah nama pengguna akun yang sedang digunakan. Pengguna memasukkan username baru, kemudian mengonfirmasi perubahan.

   ![Mengganti username](/images/Gambar7.jpeg)

8. **Mengganti password**  
   Menu **Change Password** digunakan untuk memperbarui password akun. Pengguna memasukkan password baru, kemudian mengonfirmasi perubahan.

   ![Mengganti password](/images/Gambar8.jpeg)

9. **Memutus koneksi dan keluar dari website**  
   Setelah selesai menggunakan ClipCascade, pengguna dapat menekan **Disconnect** hingga status berubah menjadi “Disconnected from WebSocket server (P2S)” untuk memutus koneksi WebSocket dari halaman tersebut. Selanjutnya, pengguna dapat memilih **Logoff** untuk keluar dari sesi saat ini atau **Logoff from All Devices** untuk mengakhiri sesi akun pada seluruh perangkat.

   ![Memutus koneksi dan keluar dari website](/images/Gambar9.jpeg)

10. **Konfirmasi log out**  
    Sebelum proses keluar dilanjutkan, website menampilkan halaman konfirmasi yang menanyakan apakah pengguna benar-benar ingin log out. Pengguna menekan tombol **Log Out** untuk mengonfirmasi tindakan tersebut.

    ![Konfirmasi log out](/images/Gambar10.jpeg)

11. **Kembali ke halaman login**  
    Setelah berhasil log out, pengguna diarahkan kembali ke halaman login. Pada contoh tangkapan layar, website menampilkan pemberitahuan bahwa sesi telah berakhir dan meminta pengguna memasukkan username serta password untuk masuk kembali.

    ![Kembali ke halaman login](/images/Gambar11.jpeg)

#### Pengujian Pemindahan Teks antar perangkat

Pengujian dilakukan menggunakan dua perangkat, yaitu Mac sebagai perangkat pengirim teks yang disalin dan Windows sebagai perangkat penerima. ClipCascade diuji melalui dua cara: pengiriman teks secara manual melalui halaman website dan sinkronisasi clipboard otomatis.

12. **Teks disalin pada perangkat pengirim**  
    Pada metode pertama, pengguna menyalin teks yang ingin dipindahkan dari perangkat Mac. Teks yang disalin ini menjadi data yang akan dikirimkan ke perangkat Windows.

    ![Teks disalin pada perangkat pengirim](/images/Gambar12.jpeg)

13. **Cara 1 : Mengirim teks melalui halaman ClipCascade**  
    Teks yang telah disalin dikirim secara manual melalui halaman ClipCascade. Setelah dikirim, data tersebut ditampilkan pada bagian Channel sebagai pesan yang diteruskan ke perangkat penerima.

    ![Mengirim teks melalui halaman ClipCascade](/images/Gambar13.jpeg)

14. **Teks tampil pada bagian Channel**  
    Pesan yang dikirim muncul pada bagian Channel di halaman ClipCascade dan dapat dilihat oleh perangkat yang terhubung. Pada metode manual, perangkat penerima Windows menyalin teks yang tampil pada Channel sebelum menggunakannya.

    ![Teks tampil pada bagian Channel](/images/Gambar14.jpeg)

15. **Menempelkan teks pada perangkat penerima**  
    Setelah teks disalin dari Channel pada perangkat Windows, pengguna menempelkannya pada aplikasi tujuan. Dalam pengujian ini, Google Docs digunakan untuk memastikan bahwa teks berhasil dipindahkan dan dapat digunakan.

    ![Menempelkan teks pada perangkat penerima](/images/Gambar15.jpeg)

16. **Cara 2 : Sinkronisasi teks secara otomatis**  
    Pada metode kedua, pengguna cukup menyalin teks pada perangkat pengirim Mac tanpa mengirimkannya secara manual melalui website. Teks yang disalin akan disinkronkan dan muncul pada perangkat lain yang terhubung, sesuai pengaturan ClipCascade.

    ![Sinkronisasi teks secara otomatis](/images/Gambar16.jpeg)

17. **Menempelkan teks tanpa membuka website**  
    Setelah sinkronisasi otomatis berlangsung, perangkat penerima Windows dapat langsung menempelkan teks ke aplikasi tujuan tanpa perlu membuka halaman ClipCascade. Pada pengujian ini, Google Docs digunakan sebagai tempat untuk menempelkan dan memeriksa hasil teks.

    ![Menempelkan teks tanpa membuka website](/images/Gambar17.jpeg)

#### Kesimpulan Pengujian

Berdasarkan pengujian pada perangkat Mac dan Windows, ClipCascade dapat digunakan untuk memindahkan teks antar perangkat melalui pengiriman manual pada halaman Channel maupun melalui sinkronisasi clipboard otomatis. Koneksi WebSocket perlu aktif agar perangkat dapat bertukar data, sedangkan fitur enkripsi membantu melindungi data clipboard selama proses sinkronisasi.

# Pembahasan

[`^ kembali ke atas ^`](#)

### Analisis ClipCascade dan Perbandingannya dengan KDE Connect

ClipCascade merupakan aplikasi _clipboard synchronization_ yang memungkinkan pengguna menyinkronkan isi clipboard secara otomatis antar perangkat, seperti Windows, macOS, Linux, dan Android. Aplikasi ini ditulis menggunakan bahasa pemrograman `Java` dan dapat dijalankan menggunakan `Docker` untuk mempermudah proses instalasi serta pengelolaan server. ClipCascade mendukung mekanisme komunikasi Peer-to-Server (P2S) dan Peer-to-Peer (P2P), serta menyediakan fitur enkripsi end-to-end untuk menjaga keamanan data. Beberapa kelebihan yang dimiliki ClipCascade antara lain:

- Mendukung sinkronisasi clipboard secara otomatis antar perangkat tanpa perlu mengirim data secara manual.
- Mendukung berbagai sistem operasi, termasuk Windows, macOS, Linux, dan Android.
- Menggunakan `Docker` untuk mempermudah proses instalasi, deployment, dan pengelolaan server.
- Memungkinkan penyimpanan data pengguna secara persisten melalui konfigurasi volume Docker.
- Menyediakan konfigurasi ukuran maksimum pesan dan mode komunikasi melalui variabel lingkungan (_environment variables_).
- Menggunakan lisensi `GPL-3.0`, sehingga kode sumbernya dapat dipelajari dan dikembangkan sesuai ketentuan lisensi tersebut.

Meskipun memiliki berbagai kelebihan, ClipCascade juga memiliki beberapa kekurangan, di antaranya:

- Membutuhkan koneksi jaringan agar sinkronisasi antar perangkat dapat berlangsung.
- Instalasi dan konfigurasi server memerlukan pemahaman dasar mengenai Docker, port jaringan, dan alamat server.
- Pada mode P2S, proses sinkronisasi bergantung pada ketersediaan server.
- Akses dari jaringan yang berbeda memerlukan konfigurasi jaringan tambahan agar server dapat dijangkau.
- Pengguna perlu memperhatikan keamanan akun, konfigurasi server, dan perlindungan data clipboard yang berpotensi memuat informasi sensitif.

Jika dibandingkan dengan aplikasi sejenis, yaitu **KDE Connect**, keduanya memiliki fungsi berbagi clipboard antar perangkat, tetapi menawarkan pendekatan yang berbeda. Berikut beberapa perbandingan antara ClipCascade dan KDE Connect:

- **ClipCascade** memungkinkan pengguna menjalankan server sendiri menggunakan Docker, sedangkan **KDE Connect** berfokus pada integrasi langsung antar perangkat dan umumnya digunakan melalui jaringan lokal.
- **ClipCascade** dapat digunakan sebagai sarana mempelajari arsitektur client-server dan konfigurasi server, sedangkan **KDE Connect** lebih menekankan integrasi berbagai fitur antar perangkat, seperti berbagi file, notifikasi, dan clipboard.
- **ClipCascade** mendukung konfigurasi mode P2S dan P2P, sedangkan **KDE Connect** mengandalkan komunikasi antar perangkat yang telah dipasangkan tanpa memerlukan server clipboard khusus.
- **ClipCascade** memberikan keleluasaan dalam mengelola server dan konfigurasi deployment, tetapi pengguna perlu mengelola sendiri ketersediaan serta keamanan servernya. Sementara itu, **KDE Connect** lebih praktis untuk penggunaan sehari-hari karena tidak mengharuskan pengguna menyiapkan server tersendiri.
- **ClipCascade** cocok digunakan untuk mempelajari deployment aplikasi berbasis Docker dan komunikasi data melalui server, sedangkan **KDE Connect** lebih cocok untuk mempelajari komunikasi antar perangkat dan integrasi layanan dalam jaringan lokal.

Dalam penerapannya pada mata kuliah Komunikasi Data dan Jaringan Komputer, ClipCascade dapat digunakan sebagai media pembelajaran untuk memahami komunikasi client-server, konfigurasi port, pertukaran data, dan pengujian konektivitas jaringan. Dengan menjalankan server menggunakan Docker dan menghubungkan klien Windows serta Android, mahasiswa dapat menguji proses sinkronisasi data dan mengamati pengaruh koneksi jaringan terhadap keberhasilan komunikasi. Aplikasi ini juga dapat dibandingkan dengan KDE Connect melalui pengujian waktu sinkronisasi, keberhasilan pengiriman data, penggunaan sumber daya, serta kemudahan konfigurasi.

Dengan demikian, ClipCascade tidak hanya berfungsi sebagai aplikasi berbagi clipboard, tetapi juga dapat dimanfaatkan sebagai sarana praktik untuk memahami penerapan komunikasi data, pengelolaan server, deployment berbasis container, dan keamanan pertukaran data dalam jaringan komputer.

# Referensi

[`^ kembali ke atas ^`](#)

1. [ClipCascade – Clipboard Synchronization Across Devices](https://github.com/Sathvik-Rao/ClipCascade) – GitHub.
2. [ClipCascade README.md](https://github.com/Sathvik-Rao/ClipCascade/blob/main/README.md) – Dokumentasi instalasi, konfigurasi server, Docker, dan aplikasi klien ClipCascade.
3. [ClipCascade Releases](https://github.com/Sathvik-Rao/ClipCascade/releases) – GitHub. Informasi versi terbaru dan berkas instalasi untuk berbagai sistem operasi.
4. [Docker Documentation](https://docs.docker.com/) – Docker. Dokumentasi resmi mengenai instalasi Docker, container, dan Docker Compose.
5. [KDE Connect](https://kdeconnect.kde.org/) – KDE. Informasi mengenai integrasi perangkat, sinkronisasi clipboard, dan berbagi berkas.
6. [KDE Connect – KDE Applications](https://apps.kde.org/id/kdeconnect/) – KDE. Informasi fitur dan dukungan platform KDE Connect.
7. [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.html) – GNU. Dokumentasi lisensi perangkat lunak ClipCascade.
