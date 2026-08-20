# Pertemuan 8: Konfigurasi Mail Server dan FTP Server

## 🎯 Tujuan Pembelajaran
- Memahami konsep dasar layanan Mail Server, termasuk protokol SMTP dan POP3 yang digunakan untuk mengirim dan menerima email.
- Mengonfigurasi Mail Server pada Cisco Packet Tracer, meliputi pengaturan IP, aktivasi layanan email, DNS, domain name, dan akun pengguna.
- Memahami konsep dasar layanan FTP Server sebagai media transfer file antar perangkat dalam jaringan.
- Mengonfigurasi FTP Server, meliputi pengaturan IP, pembuatan kredensial (username/password), dan hak akses (permissions) file.
- Melakukan pengujian end-to-end terhadap layanan Mail (kirim/terima surat) dan FTP (transfer file melalui CLI).

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas latihan praktikum
- `docs/` — berisi materi pendukung lain.

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

> **Catatan:** Karena topologi pada praktikum ini hanya terdiri dari Server–Switch–PC (tanpa router), field **Default Gateway** di atas tidak akan benar-benar terpakai — semua perangkat berada di satu jaringan/switch yang sama sehingga bisa saling berkomunikasi langsung. Kolom ini tetap diisi sebagai praktik konfigurasi standar, tapi jangan bingung kalau tidak ada perangkat dengan IP `192.168.10.1`.

---

## 📖 Materi Praktikum

### 1. Mail Server

Mail Server adalah perangkat atau layanan yang bertugas menerima, menyimpan, dan meneruskan email antar pengguna dalam suatu jaringan. Layanan ini bekerja menggunakan dua protokol utama, yaitu **SMTP (Simple Mail Transfer Protocol)** pada port default **25** yang digunakan untuk mengirim email, dan **POP3 (Post Office Protocol v3)** pada port default **110** yang digunakan untuk mengambil/menerima email dari server. Agar pengguna dapat mengirim dan menerima email antar akun, server memerlukan sebuah domain name serta akun pengguna (username) yang terdaftar pada domain tersebut.

#### A. Konfigurasi Jaringan & Aktivasi Layanan

**Langkah kerja:**

1. Klik Server0, PC0, dan PC1 satu per satu, lalu buka tab Desktop > IP Configuration pada masing-masing perangkat. Atur IP address, subnet mask, dan gateway sesuai Tabel IP di atas. Khusus untuk PC0 dan PC1, isi juga kolom DNS Server dengan 192.168.10.10 (IP Server0), agar PC dapat menerjemahkan nama domain mail.com ke alamat IP Server0 saat mengakses layanan email.

<img width="766" height="408" alt="image" src="https://github.com/user-attachments/assets/071afa03-ce0d-4892-9593-e7fe050e9ca7" />

<img width="766" height="408" alt="image" src="https://github.com/user-attachments/assets/19197aa6-cf85-423f-a927-9af44f5b2fc6" />

2. Di **Server0**, masuk ke tab **Services**, pilih menu **EMAIL** di sisi kiri.

3. Aktifkan (On) service **SMTP** dan **POP3**.

<img width="573" height="500" alt="image" src="https://github.com/user-attachments/assets/e540d504-f38e-4048-917a-87b13a9e8a7f" />

4. Masih di **Server0**, pindah ke menu **DNS**, lalu buat sebuah **A Record** dengan ketentuan:
   - **Name**: nama domain yang akan dipakai untuk email, misal `mail.com`
   - **Type**: `A Record`
   - **Address**: `192.168.10.10` (IP Server0)

<img width="887" height="712" alt="image" src="https://github.com/user-attachments/assets/d694d292-3877-4467-a4db-63c10adb86c0" />

   Klik **Add**, lalu **Save**.

   > ⚠️ **Penting:** Nilai **Name** di sini **harus sama persis** (termasuk huruf besar/kecil) dengan **Domain Name** yang akan diisi di langkah B nanti. Kalau berbeda, PC client tidak akan bisa resolve alamat mail server dan konfigurasi email akan gagal connect.

**Uji Verifikasi:**

- Status SMTP dan POP3 pada panel Services menampilkan indikator **On** (hijau), menandakan layanan sudah aktif dan siap menerima koneksi dari client.
- Pada tabel DNS Resource Records, muncul satu baris dengan **Name** sesuai domain yang dibuat, **Type: A Record**, dan **Detail: 192.168.10.10**.

**Cheatsheet:**

| Menu / Field | Fungsi |
|---|---|
| Desktop > IP Configuration | Mengatur IP address, subnet mask, dan gateway pada perangkat |
| Services > EMAIL | Menu untuk mengaktifkan dan mengatur layanan email |
| SMTP (On/Off) | Mengaktifkan protokol pengiriman email (port 25) |
| POP3 (On/Off) | Mengaktifkan protokol penerimaan email (port 110) |
| Services > DNS | Menu untuk memetakan nama domain ke IP address (A Record) |

#### B. Konfigurasi Domain & Akun Pengguna

**Langkah kerja:**

1. Masih di tab **Services > EMAIL**, isi kolom **Domain Name** dengan nilai yang **sama persis** dengan Name di DNS A Record pada langkah sebelumnya, contoh: `mail.com`, lalu klik **Set**.
2. Pada bagian **User Setup**, isi **User** dan **Password** untuk akun pertama (contoh: `roni` / `12345`), lalu klik **+** untuk menambahkan akun.
3. Ulangi untuk membuat akun kedua (contoh: `budi` / `12345`) agar bisa saling mengirim email antar PC.

<img width="896" height="714" alt="image" src="https://github.com/user-attachments/assets/461b0c4b-ff54-4b75-810f-dbfffe3635ba" />

**Uji Verifikasi:**

Daftar akun (user list) pada panel Services menampilkan seluruh username yang telah didaftarkan pada domain `mail.com`.

<img width="583" height="94" alt="image" src="https://github.com/user-attachments/assets/3911076b-9566-486f-a54e-bca4a16f746e" />

Setelah domain dan akun siap, lanjutkan konfigurasi & pengiriman email di masing-masing PC pada bagian C berikut.

<img width="112" height="156" alt="image" src="https://github.com/user-attachments/assets/3ae5dd7c-a513-42bb-bc72-0d480e7aa6b7" />

**Cheatsheet:**

| Field / Tombol | Fungsi |
|---|---|
| Domain Name | Menentukan nama domain email server, harus sama dengan Name di DNS A Record (contoh: `mail.com`) |
| Set (Domain) | Menyimpan konfigurasi domain name |
| User / Password | Membuat kredensial akun email pengguna |
| `+` (Add User) | Menambahkan akun baru ke daftar user email |
| `-` (Remove User) | Menghapus akun dari daftar user email |

#### C. Uji Coba Pengiriman & Penerimaan Email (End-to-End)

**Langkah kerja (di sisi PC client):**

1. Klik PC0, masuk ke tab **Desktop > Email**.
2. Pada konfigurasi awal (Configure Mail), isi:
   - Your Name: `roni`
   - Email Address: `roni@mail.com`
   - Incoming Mail Server: `mail.com`
   - Outgoing Mail Server: `mail.com`
   - User Name: `roni`
   - Password: `12345`

<img width="632" height="604" alt="image" src="https://github.com/user-attachments/assets/f83a3587-3890-495d-9f05-4fe496e0d35e" />

3. Klik **Save**, lalu buka menu **Compose** untuk menulis email baru ke `budi@mail.com`, isi subjek dan isi pesan, lalu klik **Send**.
4. Lakukan konfigurasi email yang sama di PC1 untuk akun `budi` (Email Address: `budi@mail.com`, User Name: `budi`, Password: `12345`).
5. Di PC1, klik **Receive** pada aplikasi Email untuk mengunduh email masuk dari server.

**Uji Verifikasi:**

Email yang dikirim dari PC0 (`roni`) muncul pada Inbox PC1 (`budi`) setelah menekan tombol **Receive**, lengkap dengan subjek dan isi pesan yang sesuai.

<img width="669" height="299" alt="image" src="https://github.com/user-attachments/assets/05657235-6878-4fe9-bfd5-4d59ce73a8c5" />

<img width="789" height="469" alt="image" src="https://github.com/user-attachments/assets/a31e43ba-0246-487c-8844-75b7e671c8bf" />

> 🔧 **Troubleshooting cepat jika gagal:**
> - Pastikan Domain Name di EMAIL service dan Name di DNS A Record **sama persis**.
> - Pastikan DNS Server di IP Configuration PC0/PC1 mengarah ke IP Server0 (`192.168.10.10`).
> - Pastikan username & password di Configure Mail sama dengan yang terdaftar di Server0 > Services > EMAIL.

---

### 2. FTP Server

FTP (File Transfer Protocol) adalah protokol jaringan yang digunakan untuk mentransfer file antara client dan server melalui jaringan TCP/IP. FTP berjalan pada port default **21** untuk kontrol koneksi. Untuk mengakses FTP Server, client memerlukan kredensial (username dan password) yang telah terdaftar pada server, serta hak akses (permissions) yang menentukan aksi apa saja yang boleh dilakukan pengguna tersebut terhadap file di server.

#### A. Konfigurasi Jaringan & Kredensial FTP

**Langkah kerja:**

1. Pastikan IP Server0 sudah dikonfigurasi.
2. Masuk ke tab **Services**, pilih menu **FTP** di sisi kiri.
3. Aktifkan (On) service FTP.
4. Isi **Username**, **Password**, **hak akses** untuk akun FTP, contoh: `roni` / `12345`, lalu klik **+** untuk menambahkan.
5. Ulangi jika ingin membuat akun FTP kedua (contoh: `budi` / `12345`).

<img width="895" height="723" alt="image" src="https://github.com/user-attachments/assets/b2e5fee6-28a1-4399-bbda-6f2768f4f402" />

**Uji Verifikasi:**

Status FTP pada panel Services menampilkan indikator **On** (hijau), dan akun yang dibuat muncul pada daftar user FTP. Pengujian koneksi lengkap melalui CLI dijelaskan pada bagian C.

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

<img width="548" height="266" alt="image" src="https://github.com/user-attachments/assets/90100614-21bd-45c7-a47e-c80f1522100e" />

**Uji Verifikasi:**

Checkbox permission pada akun FTP menampilkan tanda centang sesuai hak akses yang telah diberikan.

#### C. Uji Coba Transfer File (FTP CLI)

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

<img width="986" height="766" alt="image" src="https://github.com/user-attachments/assets/b71b6d97-ff94-4256-8894-55d0baae7841" />

**Perintah dasar FTP CLI:**

```
ftp> dir             (menampilkan daftar file di server)
ftp> put nama.txt     (mengunggah file dari PC ke server)
ftp> get nama.txt     (mengunduh file dari server ke PC)
ftp> delete nama.txt  (menghapus file di server)
ftp> quit             (keluar dari sesi FTP)
```

**Uji Verifikasi:**

Perintah `dir` menampilkan daftar file yang tersimpan di FTP Server, dan perintah `put`/`get` berhasil dijalankan tanpa pesan error (ditandai status `Transfer complete`).

<img width="517" height="316" alt="image" src="https://github.com/user-attachments/assets/aab8dde3-7b80-4050-9161-8dcb0ae7f28e" />

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

### 3. Latihan Praktikum

1. Buat topologi sederhana di Cisco Packet Tracer menggunakan 1 Server dan 2 PC (hubungkan melalui Switch)
2. Konfigurasikan IP address pada Server dan kedua PC, lalu aktifkan layanan SMTP/POP3 dan DNS pada Server

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- cisco book - taufik
