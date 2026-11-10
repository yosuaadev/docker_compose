Deployment WordPress & MariaDB dengan Docker Compose

Repositori ini dibuat untuk memenuhi penugasan praktikum Cloud Computing mengenai pengelolaan aplikasi multi-container menggunakan Docker Compose di lingkungan VPS.

Identitas Pembuat

Nama Lengkap: Yosua Ade Nugraha

Kelas: XI SIJA 1

Nomor Absen: 30

Tautan Repository GitHub: https://github.com/yosuaadev/docker_compose

Deskripsi Singkat Project

Project ini menjalankan dua layanan container utama secara bersamaan menggunakan Docker Compose, yaitu:

WordPress (wordpress:latest): Sebagai aplikasi CMS utama untuk membangun website.

MariaDB (mariadb:11): Sebagai sistem manajemen database relasional untuk menyimpan seluruh data postingan, pengguna, dan konfigurasi WordPress.

Prasyarat Perangkat

Sebelum menjalankan project ini, pastikan VPS atau komputer lokal Anda telah terpasang:

Docker Engine / Docker Desktop

Docker Compose

Struktur Folder Project

wordpress-compose-ade/
├── compose.yaml
├── .env
├── .env.example
├── README.md
└── bukti-running/
    ├── docker-ps.png
    ├── wordpress-browser.png
    └── admin-dashboard.png


Cara Menjalankan Project

Clone atau unduh repository ini ke VPS:

git clone https://github.com/yosuaadev/docker_compose.git
cd docker_compose


Buat file .env berdasarkan file contoh (.env.example):
Salin template environment, lalu sesuaikan nilai password dan nama database sesuai kebutuhan Anda.

cp .env.example .env


Jalankan container menggunakan Docker Compose:

docker compose up -d


Akses aplikasi melalui browser:
Buka alamat IP VPS atau http://localhost:8090 (sesuaikan dengan port yang dikonfigurasi).

Daftar Perintah Docker Compose

Berikut adalah perintah dasar yang digunakan selama pengelolaan container:

docker compose config: Memeriksa validitas file konfigurasi dan variabel lingkungan.

docker compose up -d: Membuat dan menjalankan container di latar belakang (background).

docker compose ps: Memeriksa status kesehatan dan daftar layanan yang sedang berjalan.

docker compose logs wordpress: Melihat log aktivitas dari container WordPress untuk keperluan debugging.

docker compose down: Menghentikan dan menghapus container yang sedang berjalan.

Tugas Eksplorasi dan Penjelasan

1. Tujuan Docker Compose & Alasan WordPress Membutuhkan Database Terpisah

Docker Compose bertujuan untuk memudahkan pengelolaan aplikasi yang terdiri dari banyak container (multi-container) cukup dengan satu file konfigurasi (compose.yaml) dan satu perintah terminal. WordPress membutuhkan layanan database terpisah karena WordPress bertindak sebagai aplikasi web (frontend/backend PHP), sementara data seperti artikel, komentar, dan akun pengguna harus disimpan secara aman dan terstruktur dalam database relasional (seperti MariaDB/MySQL). Pemisahan ini menerapkan prinsip arsitektur microservices, sehingga jika layanan web mengalami gangguan atau perlu diperbarui, data di database tetap aman dan terisolasi.

2. Fungsi Service db dan Service wordpress pada compose.yaml

Service db: Berfungsi untuk menyiapkan dan menjalankan container database MariaDB yang bertugas melayani permintaan penyimpanan serta pengambilan data dari WordPress.

Service wordpress: Berfungsi untuk menjalankan server web dan aplikasi WordPress itu sendiri agar dapat diakses oleh pengguna melalui browser.

3. Arti Atribut Konfigurasi Docker Compose

image: Menentukan versi image resmi yang diunduh dari Docker Hub (misal mariadb:11 atau wordpress:latest).

container_name: Memberikan nama kustom yang jelas dan mudah dikenali pada container yang berjalan.

restart: Mengatur kebijakan restart otomatis (contoh: unless-stopped membuat container otomatis menyala kembali jika server di-reboot atau container crash, kecuali dihentikan secara manual).

ports: Melakukan port mapping dari port di dalam container ke port di VPS/host luar agar bisa diakses dari browser (misal "8090:80").

environment: Menyediakan variabel lingkungan rahasia atau konfigurasi (seperti nama database dan password) ke dalam container.

depends_on: Mengatur urutan prioritas, memastikan service database (db) menyala terlebih dahulu sebelum service WordPress dijalankan.

volumes: Menghubungkan direktori atau penyimpanan persisten agar data di dalam database dan file web tetap tersimpan meskipun container dihentikan atau dihapus.

4. Alasan WORDPRESS_DB_HOST Menggunakan db:3306

Variabel WORDPRESS_DB_HOST diisi dengan db:3306 karena di dalam jaringan internal Docker Compose, nama service bertindak sebagai hostname. Container WordPress mengenali container database melalui nama service-nya yaitu db pada port default 3306, bukan localhost. Jika menggunakan localhost, WordPress akan mencari database di dalam container-nya sendiri dan berujung pada error koneksi.

5. Perbedaan Fungsi .env, .env.example, dan .gitignore

.env: File privat yang menyimpan data rahasia yang sebenarnya (seperti password database). File ini tidak boleh diunggah ke GitHub demi keamanan.

.env.example: File templat publik yang berisi daftar variabel tanpa nilai rahasia asli, berfungsi sebagai panduan bagi pengguna lain untuk membuat file .env mereka sendiri.

.gitignore: File konfigurasi Git yang mendaftarkan nama file atau folder agar diabaikan dan tidak ikut ter-track atau ter-push ke repository publik.

6. Pembuktian dari Percobaan docker compose down lalu docker compose up -d

Percobaan mematikan container dengan docker compose down lalu menyalakannya kembali dengan docker compose up -d membuktikan bahwa data bersifat persisten (tidak hilang). Berkat penggunaan Docker Volumes (db_data dan wordpress_data), seluruh artikel, pengaturan, dan database yang telah dibuat sebelumnya tetap tersimpan dengan aman di dalam volume server meskipun container-nya dihancurkan dan dibuat ulang.

7. Kendala Selama Setup dan Solusinya

Kendala: Terjadi konflik port (port is already allocated) pada port standar 80 karena layanan web bawaan VPS sudah lebih dulu menggunakan port tersebut.

Penyebab: Port 80 di host VPS sudah terikat oleh web server lain.

Solusi & Hasil: Melakukan modifikasi port mapping pada file compose.yaml menjadi port 8090:80. Hasilnya, WordPress dapat diakses dengan lancar melalui http://<IP-VPS>:8090 tanpa bentrok dengan layanan lain.

8. Modifikasi Mandiri yang Dilakukan

Modifikasi mandiri yang dilakukan adalah menyesuaikan port akses publik ke port 8090 serta mengatur konfigurasi nama database dan kredensial pengguna secara spesifik di dalam file .env terpisah agar lebih aman dan terstruktur sesuai standar pengelolaan server produksi.

Bukti Hasil Running

Berikut adalah dokumentasi bukti pengerjaan yang tersimpan di dalam folder bukti-running/:

docker-ps.png: Menunjukkan status container WordPress dan MariaDB berjalan dengan status Up.

wordpress-browser.png: Menampilkan halaman depan website WordPress dan artikel uji coba yang telah dibuat.

admin-dashboard.png: Menampilkan halaman dashboard administrator WordPress.
