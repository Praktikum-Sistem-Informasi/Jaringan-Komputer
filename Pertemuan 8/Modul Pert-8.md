# Pertemuan 8: Konfigurasi Mail Server dan FTP Server

## 🎯 Tujuan Pembelajaran
- Memahami konsep dasar layanan Mail Server, termasuk protokol SMTP dan POP3 yang digunakan untuk mengirim dan menerima email.
- Mengonfigurasi Mail Server pada Cisco Packet Tracer, meliputi pengaturan IP, aktivasi layanan email, domain name, dan akun pengguna.
- Memahami konsep dasar layanan FTP Server sebagai media transfer file antar perangkat dalam jaringan.
- Mengonfigurasi FTP Server, meliputi pengaturan IP, pembuatan kredensial (username/password), dan hak akses (permissions) file.
- Melakukan pengujian end-to-end terhadap layanan Mail (kirim/terima surat) dan FTP (transfer file melalui CLI).
- Mendokumentasikan hasil konfigurasi dalam bentuk laporan yang rapi disertai screenshot dan penjelasan.

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: ?
- `docs/` — berisi modul ini beserta materi pendukung lain (?).

## 🚀 Cara Menjalankan Cisco Packet Tracer
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
1. Buka aplikasi Cisco Packet Tracer (matikan jaringan internet).
2. Buat topologi jaringan baru sesuai instruksi (1 Server, 2 PC, 1 Switch).
3. Hubungkan perangkat sesuai topologi (gunakan kabel yang sesuai).
4. Klik masing-masing perangkat untuk membuka menu konfigurasi
   (tab Config/Services/Desktop) sesuai instruksi.
5. Simpan file .pkt secara berkala.
```

**Tabel IP:**

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Server0 (Mail + FTP) | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC0 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 192.168.10.3 | 255.255.255.0 | 192.168.10.1 |

---

## 📖 Materi Praktikum

### 1. Mail Server

Mail Server adalah perangkat atau layanan yang bertugas menerima, menyimpan, dan meneruskan email antar pengguna dalam suatu jaringan. Layanan ini bekerja menggunakan dua protokol utama, yaitu **SMTP (Simple Mail Transfer Protocol)** pada port default **25** yang digunakan untuk mengirim email, dan **POP3 (Post Office Protocol v3)** pada port default **110** yang digunakan untuk mengambil/menerima email dari server. Agar pengguna dapat mengirim dan menerima email antar akun, server memerlukan sebuah domain name serta akun pengguna (username) yang terdaftar pada domain tersebut.

#### A. Konfigurasi Jaringan & Aktivasi Layanan

**Langkah kerja:**

1. Klik Server0, buka tab **Desktop > IP Configuration**, lalu atur IP address, subnet mask, dan gateway sesuai tabel IP.
2. Masuk ke tab **Services**, pilih menu **EMAIL** di sisi kiri.
3. Aktifkan (On) service **SMTP** dan **POP3**.

**Uji Verifikasi:**

Status SMTP dan POP3 pada panel Services menampilkan indikator **On** (hijau), menandakan layanan sudah aktif dan siap menerima koneksi dari client.

**Cheatsheet:**

| Menu / Field | Fungsi |
|---|---|
| Desktop > IP Configuration | Mengatur IP address, subnet mask, dan gateway pada server |
| Services > EMAIL | Menu untuk mengaktifkan dan mengatur layanan email |
| SMTP (On/Off) | Mengaktifkan protokol pengiriman email (port 25) |
| POP3 (On/Off) | Mengaktifkan protokol penerimaan email (port 110) |

#### B. Konfigurasi Domain & Akun Pengguna

**Langkah kerja:**

1. Masih di tab **Services > EMAIL**, isi kolom **Domain Name**, contoh: `mail.roni.com`, lalu klik **Set**.
2. Pada bagian **User**, isi **Username** dan **Password** untuk tiap akun yang akan dibuat (contoh: `roni` / `12345`), lalu klik **+** untuk menambahkan akun.
3. Ulangi untuk membuat akun kedua (contoh: `budi` / `12345`) agar bisa saling mengirim email antar PC.

**Uji Verifikasi:**

Daftar akun (user list) pada panel Services menampilkan seluruh username yang telah didaftarkan pada domain `mail.roni.com`.

**Cheatsheet:**

| Field / Tombol | Fungsi |
|---|---|
| Domain Name | Menentukan nama domain email server (contoh: `mail.roni.com`) |
| Set (Domain) | Menyimpan konfigurasi domain name |
| Username / Password | Membuat kredensial akun email pengguna |
| `+` (Add User) | Menambahkan akun baru ke daftar user email |
| `-` (Remove User) | Menghapus akun dari daftar user email |

---

### 2. FTP Server

FTP (File Transfer Protocol) adalah protokol jaringan yang digunakan untuk mentransfer file antara client dan server melalui jaringan TCP/IP. FTP berjalan pada port default **21** untuk kontrol koneksi. Untuk mengakses FTP Server, client memerlukan kredensial (username dan password) yang telah terdaftar pada server, serta hak akses (permissions) yang menentukan aksi apa saja yang boleh dilakukan pengguna tersebut terhadap file di server.

#### A. Konfigurasi Jaringan & Kredensial FTP

**Langkah kerja:**

1. Pastikan IP Server0 sudah dikonfigurasi (satu server dapat menjalankan Mail dan FTP sekaligus).
2. Masuk ke tab **Services**, pilih menu **FTP** di sisi kiri.
3. Aktifkan (On) service FTP.
4. Isi **Username** dan **Password** untuk akun FTP, contoh: `roni` / `12345`, lalu klik **+** untuk menambahkan.

**Uji Verifikasi:**

Status FTP pada panel Services menampilkan **On**, dan akun yang baru dibuat muncul pada daftar user FTP.

**Cheatsheet:**

| Menu / Field | Fungsi |
|---|---|
| Services > FTP | Menu untuk mengaktifkan dan mengatur layanan FTP |
| FTP (On/Off) | Mengaktifkan layanan FTP (port 21) |
| Username / Password | Membuat kredensial akun FTP |
| `+` (Add User) | Menambahkan akun baru ke daftar user FTP |

#### B. Pengaturan Hak Akses File / Permissions

Setiap akun FTP memiliki checkbox permission yang menentukan aksi yang diizinkan:

| Permission | Fungsi |
|---|---|
| `Write` | Mengizinkan pengguna mengunggah (upload) file ke server |
| `Read` | Mengizinkan pengguna mengunduh (download) file dari server |
| `Delete` | Mengizinkan pengguna menghapus file di server |
| `Rename` | Mengizinkan pengguna mengubah nama file di server |
| `List` | Mengizinkan pengguna melihat daftar file/direktori di server |

**Langkah kerja:**

1. Pada baris akun FTP yang telah dibuat, centang permission sesuai kebutuhan (misalnya `Write`, `Read`, `Delete`, `Rename`, `List` seluruhnya dicentang untuk akses penuh).
2. Simpan konfigurasi (klik **Save**, jika tersedia).

**Uji Verifikasi:**

Checkbox permission pada akun FTP menampilkan tanda centang sesuai hak akses yang telah diberikan.

---

### 3. Uji Coba Layanan End-to-End

#### A. Pengujian Surat (Mail)

**Langkah kerja (di sisi PC client):**

1. Klik PC0, masuk ke tab **Desktop > Email**.
2. Pada konfigurasi awal (Configure Mail), isi:
   - Your Name: `roni`
   - Email Address: `roni@mail.roni.com`
   - Incoming Mail Server: `192.168.10.10`
   - Outgoing Mail Server: `192.168.10.10`
   - User Name: `roni`
   - Password: `12345`
3. Klik **Save**, lalu buka menu **Compose** untuk menulis email baru ke `budi@mail.roni.com`, isi subjek dan isi pesan, lalu klik **Send**.
4. Lakukan konfigurasi email yang sama di PC1 untuk akun `budi`.
5. Di PC1, klik **Receive** pada aplikasi Email untuk mengunduh email masuk dari server.

**Uji Verifikasi:**

Email yang dikirim dari PC0 (`roni`) muncul pada Inbox PC1 (`budi`) setelah menekan tombol **Receive**, lengkap dengan subjek dan isi pesan yang sesuai.

#### B. Pengujian Transfer File (FTP CLI)

**Langkah kerja (di sisi PC client, via Command Prompt):**

```
C:\>ftp 192.168.10.10
Connected to 192.168.10.10
220- Welcome to PT Ftp server
Username: roni
331- Username ok, need password
Password: 
230- Logged in
(passive mode On)
ftp>
```

**Perintah dasar FTP CLI:**

```
ftp> dir             (menampilkan daftar file di server)
ftp> put nama.txt     (mengunggah file dari PC ke server)
ftp> get nama.txt     (mengunduh file dari server ke PC)
ftp> delete nama.txt  (menghapus file di server)
ftp> quit             (keluar dari sesi FTP)
```

**Uji Verifikasi:**

Perintah `dir` menampilkan daftar file yang tersimpan di FTP Server, dan perintah `put`/`get` berhasil dijalankan tanpa pesan error (ditandai status `226 Transfer complete`).

**Cheatsheet CLI FTP:**

| Perintah | Fungsi |
|---|---|
| `ftp <ip>` | Membuka koneksi FTP ke server tujuan |
| `dir` | Menampilkan daftar file/direktori di server |
| `put <namafile>` | Mengunggah file dari client ke server |
| `get <namafile>` | Mengunduh file dari server ke client |
| `delete <namafile>` | Menghapus file di server |
| `quit` | Menutup sesi FTP |

---

### 4. Latihan Praktikum

1. Buat topologi sederhana di Cisco Packet Tracer menggunakan **1 Server** dan **2 PC** (hubungkan melalui Switch)!
2. Konfigurasikan IP address pada Server dan kedua PC, lalu aktifkan layanan **SMTP/POP3** dan **FTP** pada Server!
3. Buat **2 akun pengguna** (untuk email dan FTP) dengan username & password bebas (contoh: `roni`/`12345` dan `budi`/`12345`)!
4. Kirim satu email dari PC0 ke PC1, lalu screenshot hasil email yang diterima di Inbox PC1!
5. Login FTP dari salah satu PC menggunakan CLI, lalu screenshot hasil perintah `dir` yang menampilkan isi server!

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- cisco book - taufik
