# Pertemuan 7: Konfigurasi WLAN, Keamanan CLI, dan Remote Access

## 🎯 Tujuan Pembelajaran
- Memahami konsep dasar jaringan nirkabel (WLAN) dan cara kerja Access Point dalam menyediakan koneksi wireless.
- Mengonfigurasi Access Point, meliputi pengaturan nama jaringan (SSID) dan keamanan jaringan menggunakan WPA2-PSK.
- Menghubungkan perangkat client seperti smartphone atau laptop ke jaringan WLAN dengan konfigurasi SSID dan password yang sesuai.
- Memastikan client mendapatkan alamat IP secara otomatis melalui layanan DHCP.
- Menerapkan keamanan dasar pada Cisco Router, khususnya pengamanan akses console dan Privilege Mode.
- Memahami dan mengonfigurasi remote access menggunakan Telnet dan SSH.
- Membedakan Telnet dan SSH, terutama dari aspek keamanan dan enkripsi komunikasi.

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: ?
- `docs/` — berisi modul ini beserta materi pendukung lain (?).

## 🚀 Cara Menjalankan Cisco Packet Trace
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
1. Buka aplikasi Cisco Packet Tracer (matikan jaringan internet).
2. Buat topologi jaringan baru sesuai intruksi
3. Hubungkan perangkat sesuai topologi (gunakan kabel yang sesuai ).
4. Klik masing-masing perangkat untuk membuka menu konfigurasi 
   (tab Config/GUI/CLI) sesuai intruksi.
5. Simpan file .pkt secara berkala.
```

## 📖 Materi Praktikum

### 1. WLAN (Wireless Local Area Network)
 
WLAN (Wireless Local Area Network) adalah sistem jaringan komputer yang mencakup area lokal tertentu seperti gedung perkantoran, kampus, atau rumah tanpa menggunakan sambungan kabel fisik untuk menghubungkan perangkat-perangkat di dalamnya. Sebagai gantinya, WLAN memanfaatkan teknologi frekuensi radio untuk mengirimkan dan menerima data melalui medium udara. Frekuensi yang paling umum digunakan berada pada pita gelombang radio 2,4 GHz dan 5 GHz, yang beroperasi berdasarkan standar teknis IEEE 802.11, atau yang secara komersial lebih akrab kita sebut dengan Wi-Fi.
 
Dalam cara kerjanya, infrastruktur WLAN sangat mengandalkan perangkat pusat yang disebut Access Point (AP) atau wireless router. Perangkat ini berfungsi sebagai stasiun pemancar dan penerima (transceiver) yang menjembatani lalu lintas data antara perangkat nirkabel pengguna — seperti laptop, tablet, dan ponsel pintar — dengan jaringan kabel utama atau koneksi penyedia internet (ISP). Ketika pengguna berpindah tempat di dalam batas area cakupan sinyal Access Point tersebut, koneksi perangkat akan tetap terjaga secara otomatis. Hal ini memberikan tingkat mobilitas, skalabilitas, dan fleksibilitas tinggi yang tidak bisa ditawarkan oleh jaringan berbasis kabel (LAN) tradisional.

**Langkah kerja:**
 
<img width="1438" height="571" alt="image" src="https://github.com/user-attachments/assets/928db12c-1df9-4694-9605-cadd0495e52b" />

Secara bawaan (default), jaringan pada Access Point terbuka tanpa keamanan. Oleh karena itu, kita perlu mengatur konfigurasi SSID, sandi, dan jenis enkripsinya seperti pada gambar berikut:

<img width="320" height="258" alt="image" src="https://github.com/user-attachments/assets/d61a1077-718f-4845-a29a-b941f67ae841" /> 

<br>

<img width="573" height="297" alt="Konfigurasi SSID dan enkripsi WPA2-PSK pada Access Point" src="https://github.com/user-attachments/assets/97c61a18-4e2e-4905-be48-3fbf1b8fc46f" />

Langkah selanjutnya, buka menu Config pada Smartphone dan pilih antarmuka Wireless0. Hubungkan perangkat ke jaringan Access Point dengan memasukkan kata sandi yang telah dikonfigurasi sebelumnya, seperti pada gambar berikut:

 <img width="573" height="551" alt="image" src="https://github.com/user-attachments/assets/2794f37d-7fa3-4188-bd31-5c59b6d29b27" />

Berbeda dengan perangkat Smartphone yang sudah memiliki fitur nirkabel bawaan, pada perangkat Laptop kita harus menyesuaikan modul fisiknya terlebih dahulu. Masuk ke tab Physical, matikan daya laptop, lepaskan modul LAN bawaan, dan ganti dengan modul wireless WPC300N seperti pada gambar berikut:

<img width="576" height="303" alt="image" src="https://github.com/user-attachments/assets/f7ff1660-355c-4823-a661-fba1aa642b9c" />

Langkah selanjutnya, bisa melakukan konfigurasi seperti pada smartphone :

<img width="573" height="551" alt="image" src="https://github.com/user-attachments/assets/6611b8eb-1eee-486b-94ac-61a98c554fde" />

---

### 2. Keamanan CLI (Command Line Interface)

<img width="159" height="163" alt="image" src="https://github.com/user-attachments/assets/442a3aa1-ed71-4fae-9ebd-dab98371f7fa" />

Konsep ini mengamankan akses ke router/switch Cisco melalui CLI, supaya tidak sembarang orang bisa masuk dan mengubah konfigurasi perangkat.
 
#### A. Pengamanan Konsol Fisik (`line console 0`)
 
Pengamanan konsol fisik bertujuan untuk mencegah orang yang memiliki akses langsung ke perangkat router atau switch melalui port Console masuk dan melakukan konfigurasi tanpa izin. Dengan memberikan password pada line console 0, setiap kali seseorang mengakses perangkat melalui koneksi console, perangkat akan meminta password terlebih dahulu.

**Langkah kerja:**

```
Router>en
Router#conf t
Router(config)#line console 0
Router(config-line)#password 12345
Router(config-line)#login
Router(config-line)#exit
Router(config)#
```

**Uji Verifikasi:**

Ketika konfigurasi berhasil maka saat membuka perangkat akan diminta password

```
User Access Verification

Password:

Router>en
Router#show running-config | section line con
line con 0
 password 12345
 login
Router#
```

**Cheatsheet CLI**

| Perintah | Fungsi |
|---|---|
| `line console 0` | Masuk ke mode konfigurasi line console (0 = console pertama/satu-satunya di kebanyakan device) |
| `password 12345` | Menentukan password yang harus dimasukkan saat login |
| `login` | **Wajib** ada — perintah ini yang mengaktifkan pengecekan password. Tanpa `login`, password yang di-set tidak akan pernah diminta |
| `exit` | Keluar dari mode line console |
| `show running-config \| section line con` | Menampilkan (verifikasi) bagian konfigurasi `line console` saja dari running-config |
 
---

#### B. Pengamanan Privilege Mode (`enable secret`)

Privilege Mode adalah mode dengan hak akses tinggi yang ditandai dengan prompt `Router#`

Pada mode ini pengguna dapat menjalankan perintah administrasi dan konfigurasi penting pada perangkat. Oleh karena itu, akses ke mode ini perlu diamankan menggunakan `enable secret`.

**Langkah kerja:**

```
Router>en
Router#conf t
Router(config)#enable secret 67890
Router(config)#
```
**Uji Verifikasi:**

Ketika pengguna menjalankan `enable` router akan meminta password sebelum memberikan akses ke Privileged Mode.

```
Router>en
Password:
Router#show running-config | include enable secret
enable secret 5 $1$mERr$QIs3x0SqQACMwbwSHN8Y7.
Router#
```

**Cheatsheet CLI**

| Perintah | Fungsi |
|---|---|
| `enable secret 12345` | Menentukan password untuk mengamankan akses ke Privilege EXEC Mode (`Router#`) |
| `enable` | Masuk dari User EXEC Mode (`Router>`) ke Privilege EXEC Mode (`Router#`) |
| `show running-config \| include enable secret` | Menampilkan (verifikasi) konfigurasi `enable secret` yang tersimpan di running-config |
| `no enable secret` | Menghapus konfigurasi `enable secret` |

---

#### C. Konfigurasi Hostname

Konfigurasi hostname digunakan untuk **mengubah nama perangkat Cisco**, sehingga identitas router atau switch lebih mudah dikenali pada CLI. Secara default, nama perangkat adalah `Router` atau `Switch`.

**Langkah kerja:**

```text
Router#conf t
Router(config)#hostname roni
roni(config)#ex
roni#
````

**Uji Verifikasi:**

Setelah hostname diubah, prompt CLI akan berubah dari `Router#` menjadi `roni#`.

```text
roni#show running-config | include hostname
hostname roni
roni#
```

**Cheatsheet CLI**

| Perintah | Fungsi |
| ----|---|
| `hostname roni`                           | Mengubah nama perangkat menjadi `roni`        |
| `show running-config \| include hostname` | Menampilkan (verifikasi) konfigurasi hostname |
| `no hostname`                             | Mengembalikan hostname ke default `Router`    |

---

### 3. Remote Access (Telnet dan SSH)

#### A. Telnet

Telnet (Telecommunication Network) adalah salah satu protokol jaringan tertua yang digunakan untuk tujuan serupa, yaitu mengakses dan mengontrol perangkat dari jarak jauh. Telnet mulai digunakan sejak tahun 1969, jauh sebelum SSH diciptakan, dan berjalan pada port default 23. Protokol ini memungkinkan pengguna melakukan remote login, menjalankan perintah, serta mengonfigurasi perangkat jaringan seperti router dan switch dari jarak jauh.
Kelemahan utama Telnet terletak pada sisi keamanannya. Seluruh data yang dikirim melalui protokol ini, termasuk username dan password, dikirim dalam bentuk plain text tanpa enkripsi sama sekali. Akibatnya, data tersebut sangat rentan disadap (sniffing) oleh pihak lain yang berada di jalur jaringan yang sama, misalnya menggunakan tools seperti Wireshark. Informasi sensitif seperti password pun bisa dengan mudah terbaca oleh pihak yang tidak berwenang. Karena kelemahan inilah, Telnet kini sudah jarang digunakan dan telah digantikan oleh SSH.

**Langkah kerja:**
**Uji Verifikasi:**
**Cheatsheet CLI**
### B. SSH
SSH (Secure Shell) adalah protokol jaringan yang digunakan untuk mengakses dan mengontrol perangkat atau server dari jarak jauh secara aman. Protokol ini dikembangkan pada tahun 1995 sebagai pengganti Telnet dan rlogin, yang sebelumnya mengirim data dalam bentuk teks biasa (plain text) sehingga rentan disadap. SSH mengatasi masalah ini dengan mengenkripsi seluruh data yang dipertukarkan antara client dan server. Dengan begitu, meskipun data disadap, isinya tetap tidak dapat dibaca. SSH umumnya berjalan pada port default 22.
Dengan SSH, pengguna dapat melakukan login jarak jauh ke suatu perangkat dan menjalankan perintah sistem seolah-olah berada langsung di depan perangkat tersebut. SSH juga memungkinkan transfer file secara aman melalui SCP atau SFTP, serta port forwarding/tunneling. Proses autentikasinya bisa menggunakan kombinasi username-password, atau menggunakan SSH key pair (public key dan private key) yang lebih aman karena tidak mudah dibobol. SSH banyak digunakan oleh administrator jaringan dan sistem, termasuk untuk mengelola perangkat seperti router dan switch Cisco. Karena itu, SSH menjadi salah satu fondasi penting dalam keamanan siber dan pengelolaan infrastruktur TI.

**Langkah kerja:**
**Uji Verifikasi:**
**Cheatsheet CLI**

#### C. Perbandingan Telnet dan SSH
- **Metode Autentikasi:** Telnet hanya mengandalkan satu metode, yaitu kombinasi username dan password yang dikirim secara langsung tanpa perlindungan apa pun. Metode ini rentan terhadap serangan brute force maupun penyadapan langsung karena password terlihat jelas saat dikirim. SSH menawarkan fleksibilitas dan keamanan yang lebih baik. Selain bisa menggunakan password, SSH juga mendukung autentikasi dengan SSH key pair. Metode berbasis key ini jauh lebih aman karena private key tidak pernah dikirim melalui jaringan, sehingga risiko pencurian kredensial berkurang signifikan dibanding Telnet.
- **Integritas Data (Data Integrity):** Telnet tidak memiliki mekanisme untuk memverifikasi apakah data yang diterima masih sama persis dengan data yang dikirim. Akibatnya, data berpotensi dimodifikasi di tengah jalan tanpa terdeteksi, misalnya melalui serangan man-in-the-middle. SSH dilengkapi dengan mekanisme pengecekan integritas data menggunakan algoritma hashing. Jika ada data yang diubah atau dimanipulasi selama proses pengiriman, hal tersebut dapat terdeteksi dan koneksi bisa langsung diputus demi keamanan.
- **Verifikasi Identitas Server:** Saat menggunakan Telnet, client tidak memiliki cara untuk memastikan bahwa server yang dihubungi benar-benar server yang sah. Kondisi ini membuat Telnet rentan terhadap serangan seperti spoofing, di mana penyerang menyamar sebagai server tujuan. SSH menggunakan sistem host key untuk memverifikasi identitas server setiap kali koneksi dibuat. Jika host key berubah secara mencurigakan, SSH akan memberi peringatan kepada pengguna. Dengan begitu, potensi serangan penyamaran server bisa dicegah lebih awal.
- **Performa dan Overhead:** Dari segi kecepatan koneksi murni, Telnet sedikit lebih ringan karena tidak melakukan proses enkripsi/dekripsi. Secara teori, Telnet sedikit lebih cepat dan menggunakan resource lebih rendah. SSH memiliki overhead tambahan karena proses enkripsi, pertukaran kunci (key exchange), dan verifikasi integritas data. Namun, perbedaan performa ini biasanya tidak terlalu signifikan pada penggunaan modern, dan jauh sebanding dengan manfaat keamanan yang didapat.
- **Fitur Tambahan:** Telnet hanya berfungsi sebagai media akses command-line jarak jauh dan tidak memiliki fitur tambahan lain. SSH mendukung berbagai fitur tambahan yang lebih lengkap. Contohnya adalah transfer file secara aman melalui SCP (Secure Copy Protocol) dan SFTP (SSH File Transfer Protocol), port forwarding/tunneling untuk mengamankan aplikasi lain, serta kompresi data yang bisa mempercepat transfer pada koneksi lambat.
- **Penggunaan pada Perangkat Jaringan (Cisco):** Pada perangkat jaringan seperti router dan switch Cisco, Telnet umumnya diaktifkan menggunakan perintah dasar seperti line vty 0 4 dan password, tanpa memerlukan konfigurasi tambahan yang rumit. SSH memerlukan konfigurasi tambahan yang lebih kompleks. Konfigurasi ini meliputi pengaturan hostname, domain-name, generate RSA key (crypto key generate rsa), serta pengaktifan transport input ssh. Meski lebih rumit, hasilnya adalah akses remote yang jauh lebih aman untuk mengelola perangkat jaringan tersebut.


### 6. Tugas & Evaluasi Praktikum
1. Rancang topologi di Cisco Packet Tracer menggunakan 3 Switch dan 6 PC!
2. Konfigurasikan switch pertama sebagai VTP Server, switch kedua sebagai VTP Transparent, dan switch ketiga sebagai VTP Client!
3. Buat VLAN 10 (Marketing) dan VLAN 20 (Sales) di Switch Server!
4. Lakukan verifikasi, apakah VLAN tersebut sukses didistribusikan ke VTP Client? Jelaskan analisis Anda mengapa switch transparent tidak memiliki VLAN tersebut!

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- cisco book - taufik
