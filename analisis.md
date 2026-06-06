# Analisis Perbaikan

## Permasalahan 1

### Gejala
`docker compose up` langsung gagal dengan error parsing file `docker-compose.yml`.

### Penyebab
Baris `services` tidak memiliki tanda titik dua (`:`) di akhir kata kunci, sehingga YAML parser tidak dapat membaca struktur file dengan benar.

### Solusi
Mengubah `services` menjadi `services:` agar sintaks YAML valid.

---

## Permasalahan 2

### Gejala
Container `web1` gagal terhubung ke database dan menampilkan error koneksi saat diakses.

### Penyebab
Nilai `DB_HOST` pada service `web1` di `docker-compose.yml` diisi dengan `mysql`, padahal nama service database yang didefinisikan dalam compose file adalah `db`. Docker Compose menggunakan nama service sebagai hostname antar container.

### Solusi
Mengubah nilai `DB_HOST` pada service `web1` dari `mysql` menjadi `db`.

---

## Permasalahan 3

### Gejala
Container `web2` gagal terhubung ke database meskipun service database berjalan normal.

### Penyebab
Nilai `DB_PASS` pada service `web2` diisi dengan `wrongpassword`, sedangkan password yang didefinisikan pada service `db` adalah `student123`.

### Solusi
Mengubah nilai `DB_PASS` pada service `web2` dari `wrongpassword` menjadi `student123`.

---

## Permasalahan 4

### Gejala
Docker Compose gagal melakukan build untuk service `web3` dengan error "build context not found".

### Penyebab
Nilai `context` pada bagian `build` service `web3` diisi dengan `./web33`, padahal folder yang tersedia adalah `./web3`. Terdapat kesalahan penulisan nama folder (kelebihan satu huruf `3`).

### Solusi
Mengubah `context: ./web33` menjadi `context: ./web3`.

---

## Permasalahan 5

### Gejala
Nginx tidak dapat mem-proxy request ke `web3`, sehingga load balancing tidak berjalan sempurna karena hanya `web1` dan `web2` yang dapat dijangkau.

### Penyebab
Service `web3` di `docker-compose.yml` hanya terhubung ke network `backend`, tidak ke network `frontend`. Sementara Nginx hanya terhubung ke network `frontend`, sehingga tidak dapat berkomunikasi dengan `web3`.

### Solusi
Menambahkan network `frontend` pada service `web3` agar Nginx dapat menjangkau container tersebut.

---

## Permasalahan 6

### Gejala
Docker Compose menampilkan warning atau error terkait volume yang tidak terdefinisi.

### Penyebab
Terdapat ketidakkonsistenan nama volume: service `db` menggunakan `db-data` pada bagian `volumes`, namun pada bagian deklarasi `volumes` di level atas didefinisikan `database-data`. Kedua nama tersebut berbeda sehingga Docker tidak dapat mencocokkan volume yang dimaksud.

### Solusi
Menyeragamkan nama volume menjadi `db-data` pada deklarasi level atas maupun pada referensi di service `db`.

---

## Permasalahan 7

### Gejala
Container `nginx-lb` gagal start dengan error konfigurasi Nginx tidak valid.

### Penyebab
File `nginx/nginx.conf` diawali dengan ` ```nginx ` dan diakhiri ` ``` ` (markdown code fence). Karakter-karakter tersebut bukan bagian dari sintaks konfigurasi Nginx, sehingga Nginx parser menolak file tersebut.

### Solusi
Menghapus baris ` ```nginx ` di awal dan ` ``` ` di akhir file, sehingga hanya menyisakan konten konfigurasi Nginx yang valid.

---

## Permasalahan 8

### Gejala
Nginx gagal mem-proxy request ke `web1` dan `web3`, sehingga sebagian besar request menghasilkan error `502 Bad Gateway`.

### Penyebab
Terdapat dua kesalahan pada blok `upstream` di `nginx.conf`:
1. Nama server `web1` ditulis sebagai `web11` (kelebihan satu karakter `1`), sehingga Nginx mencoba menghubungi host yang tidak ada.
2. Port `web3` ditulis sebagai `8080`, padahal Apache di dalam container mendengarkan di port `80` (port internal container).

### Solusi
Mengubah `web11:80` menjadi `web1:80` dan `web3:8080` menjadi `web3:80`.

---

## Permasalahan 9

### Gejala
Build image untuk service `web1` gagal karena Docker tidak dapat menemukan base image yang diminta.

### Penyebab
`web1/Dockerfile` menggunakan `FROM php:8.2-apach` — nama image tidak lengkap (kurang huruf `e` di akhir). Image dengan nama tersebut tidak tersedia di Docker Hub.

### Solusi
Mengubah `FROM php:8.2-apach` menjadi `FROM php:8.2-apache`.

---

## Permasalahan 10

### Gejala
Build image untuk service `web3` gagal karena Docker tidak dapat menemukan base image yang diminta.

### Penyebab
`web3/Dockerfile` menggunakan `FROM php:8.2-apche` — nama image salah ketik (huruf `a` dan `c` tertukar). Image dengan nama tersebut tidak tersedia di Docker Hub.

### Solusi
Mengubah `FROM php:8.2-apche` menjadi `FROM php:8.2-apache`.

---

## Permasalahan 11

### Gejala
Saat mengakses aplikasi melalui Nginx, response dari `web2` menampilkan label container `WEB-WEB` alih-alih `WEB-2`.

### Penyebab
Nilai pada elemen `<strong>` di `web2/index.php` diisi dengan teks `WEB-WEB`, bukan `WEB-2`. Ini kemungkinan merupakan kesalahan copy-paste saat menyiapkan file.

### Solusi
Mengubah `WEB-WEB` menjadi `WEB-2` pada `web2/index.php`.

---

## Permasalahan 12

### Gejala
Saat mengakses aplikasi melalui Nginx, response dari `web3` menampilkan label container `WEB-WOB` alih-alih `WEB-3`.

### Penyebab
Nilai pada elemen `<strong>` di `web3/index.php` diisi dengan teks `WEB-WOB`, bukan `WEB-3`. Terdapat kesalahan pengetikan pada nama label container.

### Solusi
Mengubah `WEB-WOB` menjadi `WEB-3` pada `web3/index.php`.

---

## Permasalahan 13

### Gejala
Data identitas praktikan tidak muncul pada halaman web, dan koneksi ke database berpotensi gagal saat inisialisasi.

### Penyebab
File `db/init.sql` diawali dengan ` ```sql ` dan diakhiri ` ``` ` (markdown code fence). MySQL tidak dapat mengeksekusi file SQL yang mengandung karakter tersebut, sehingga tabel dan data awal tidak terbuat dengan benar.



### Solusi
Menghapus baris ` ```sql ` di awal dan ` ``` ` di akhir file `init.sql`, sehingga hanya menyisakan perintah SQL yang valid.

### Dokumentasi
<img width="956" height="549" alt="image" src="https://github.com/user-attachments/assets/4adb5fed-1d0b-4338-8237-1cdd2b183e44" />
<img width="701" height="536" alt="image" src="https://github.com/user-attachments/assets/fe1cdad9-9ca8-4685-aef1-3309423c1cb2" />
<img width="959" height="599" alt="image" src="https://github.com/user-attachments/assets/aa4937fa-cae0-4322-b864-dadf398b6614" />
<img width="958" height="599" alt="image" src="https://github.com/user-attachments/assets/09ad1f19-7117-440c-86de-b42d36de9efe" />
<img width="958" height="599" alt="image" src="https://github.com/user-attachments/assets/b6805029-7594-4ab6-8ac9-d37e6fa8981d" />
