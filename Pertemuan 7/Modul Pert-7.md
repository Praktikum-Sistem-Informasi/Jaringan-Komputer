# Pertemuan 7: Konfigurasi WLAN, Keamanan CLI, Remote Access, dan ACL

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
- `soal/` — berisi skenario tugas
- `docs/` — berisi materi pendukung lain.

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

<img width="1438" height="571" alt="image" src="https://github.com/user-attachments/assets/928db12c-1df9-4694-9605-cadd0495e52b" />

**Tabel IP:**

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Smartphone0 | 192.168.10.2 | 255.255.255.0| 192.168.10.1 |
| Laptop0 | 192.168.10.3 | 255.255.255.0 | 192.168.10.1 |
| PC0 | 192.168.10.4 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 192.168.10.5 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 192.168.20.2 | 255.255.255.0 | 192.168.20.1 |

**Langkah kerja:**

Konfigurasi IP di switch:

```
Switch>enable
Switch#configure terminal
Switch(config)#interface vlan 1
Switch(config-if)#ip address 192.168.10.254 255.255.255.0
Switch(config-if)#no shutdown
```
Konfigurasi Telnet di switch:

```
Switch>enable
Switch#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#line vty 0 4
Switch(config-line)#transport input telnet
Switch(config-line)#password 12345
Switch(config-line)#login
Switch(config-line)#exit
Switch(config)#enable secret 67890
Switch(config)#exit

```
**Uji Verifikasi:**

Buka command prompt di salah satu perangkat lalu masuk kedalam telnet menggunakan IP

<img width="1045" height="306" alt="image" src="https://github.com/user-attachments/assets/8b25cf8e-e1ce-42e1-a93e-32d71f106c57" />


```
Cisco Packet Tracer PC Command Line 1.0
C:\>telnet 192.168.10.254
Trying 192.168.10.254 ...Open


User Access Verification

Password: 
Switch>en
Password: 
Switch#
```
**Cheatsheet CLI**

Berikut cheatsheet CLI untuk konfigurasi Telnet:

| Perintah | Fungsi |
|---|---|
| `enable` | Masuk ke privileged EXEC mode |
| `configure terminal` | Masuk ke global configuration mode |
| `interface vlan 1` | Masuk ke konfigurasi interface VLAN 1 (untuk switch) |
| `ip address <ip> <subnet>` | Memberikan alamat IP pada interface |
| `no shutdown` | Mengaktifkan interface |
| `line vty 0 4` | Masuk ke konfigurasi line VTY (virtual terminal) 0 sampai 4, untuk mengatur akses remote |
| `transport input telnet` | Membatasi jenis protokol remote access yang diizinkan pada line VTY hanya Telnet |
| `password <password>` | Mengatur password untuk login melalui line VTY |
| `login` | Mengaktifkan permintaan password saat login |
| `enable secret <password>` | Mengatur password terenkripsi untuk masuk ke privileged EXEC mode |
| `exit` | Keluar dari mode konfigurasi saat ini |
| `telnet <ip>` | Melakukan koneksi remote ke perangkat tujuan menggunakan Telnet |
| `en` | Singkatan dari `enable`, masuk ke privileged EXEC mode |


### B. SSH
SSH (Secure Shell) adalah protokol jaringan yang digunakan untuk mengakses dan mengontrol perangkat atau server dari jarak jauh secara aman. Protokol ini dikembangkan pada tahun 1995 sebagai pengganti Telnet dan rlogin, yang sebelumnya mengirim data dalam bentuk teks biasa (plain text) sehingga rentan disadap. SSH mengatasi masalah ini dengan mengenkripsi seluruh data yang dipertukarkan antara client dan server. Dengan begitu, meskipun data disadap, isinya tetap tidak dapat dibaca. SSH umumnya berjalan pada port default 22.
Dengan SSH, pengguna dapat melakukan login jarak jauh ke suatu perangkat dan menjalankan perintah sistem seolah-olah berada langsung di depan perangkat tersebut. SSH juga memungkinkan transfer file secara aman melalui SCP atau SFTP, serta port forwarding/tunneling. Proses autentikasinya bisa menggunakan kombinasi username-password, atau menggunakan SSH key pair (public key dan private key) yang lebih aman karena tidak mudah dibobol. SSH banyak digunakan oleh administrator jaringan dan sistem, termasuk untuk mengelola perangkat seperti router dan switch Cisco. Karena itu, SSH menjadi salah satu fondasi penting dalam keamanan siber dan pengelolaan infrastruktur TI.

**Langkah kerja:**

Konfigurasi interface Router sebagai gateway masing-masing subnet:

```
Router>en
Router#conf t
Router(config)#interface gig0/0
Router(config-if)#ip address 192.168.10.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#interface gig0/1
Router(config-if)#ip address 192.168.20.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
```
Konfigurasi IP di switch:

```
Switch>enable
Switch#configure terminal
Switch(config)#interface vlan 1
Switch(config-if)#ip address 192.168.10.253 255.255.255.0
Switch(config-if)#no shutdown
```


Konfigurasi SSH di router:

```
Router(config)#username roni secret 67890
Router(config)#hostname roni
roni(config)#ip domain-name roni.org
roni(config)#crypto key generate rsa
How many bits in the modulus [512]: 512
roni(config)#line vty 0 4
roni(config-line)#transport input ssh
roni(config-line)#login local
roni(config-line)#exit
roni(config)#enable secret 67890
roni(config)#exit
```

**Uji Verifikasi:**

Buka command prompt di salah satu perangkat lalu masuk kedalam SSH menggunakan `ssh -l [host name IP address]`

<img width="1045" height="306" alt="image" src="https://github.com/user-attachments/assets/8b25cf8e-e1ce-42e1-a93e-32d71f106c57" />

```
C:\>ssh -l roni 192.168.10.1

Password: 



roni>enable
Password: 
roni#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
roni(config)#
roni#
```
**Cheatsheet CLI**
Berikut cheatsheet CLI untuk konfigurasi SSH:

| Perintah | Fungsi |
|---|---|
| `enable` / `en` | Masuk ke privileged EXEC mode |
| `configure terminal` / `conf t` | Masuk ke global configuration mode |
| `interface gig0/0` | Masuk ke konfigurasi interface tertentu pada router |
| `ip address <ip> <subnet>` | Memberikan alamat IP pada interface |
| `no shutdown` | Mengaktifkan interface |
| `username <nama> secret <pw>` | Membuat akun user lokal beserta passwordnya untuk autentikasi SSH |
| `hostname <nama>` | Mengganti nama perangkat (device name), wajib diubah dari default sebelum generate RSA key |
| `ip domain-name <domain>` | Menentukan domain name perangkat, dibutuhkan untuk proses generate RSA key |
| `crypto key generate rsa` | Membuat pasangan kunci enkripsi RSA yang digunakan SSH untuk mengamankan koneksi |
| `line vty 0 4` | Masuk ke konfigurasi line VTY 0–4 untuk mengatur akses remote |
| `transport input ssh` | Membatasi jenis protokol remote access yang diizinkan pada line VTY hanya SSH |
| `login local` | Mengaktifkan autentikasi menggunakan username & password lokal (bukan hanya password) |
| `enable secret <password>` | Mengatur password terenkripsi untuk masuk ke privileged EXEC mode |
| `exit` | Keluar dari mode konfigurasi saat ini |
| `ssh -l <username> <ip>` | Melakukan koneksi remote ke perangkat tujuan menggunakan SSH dengan username tertentu |

#### C. Perbandingan Telnet dan SSH
- **Metode Autentikasi:** Telnet hanya mengandalkan satu metode, yaitu kombinasi username dan password yang dikirim secara langsung tanpa perlindungan apa pun. Metode ini rentan terhadap serangan brute force maupun penyadapan langsung karena password terlihat jelas saat dikirim. SSH menawarkan fleksibilitas dan keamanan yang lebih baik. Selain bisa menggunakan password, SSH juga mendukung autentikasi dengan SSH key pair. Metode berbasis key ini jauh lebih aman karena private key tidak pernah dikirim melalui jaringan, sehingga risiko pencurian kredensial berkurang signifikan dibanding Telnet.
- **Integritas Data (Data Integrity):** Telnet tidak memiliki mekanisme untuk memverifikasi apakah data yang diterima masih sama persis dengan data yang dikirim. Akibatnya, data berpotensi dimodifikasi di tengah jalan tanpa terdeteksi, misalnya melalui serangan man-in-the-middle. SSH dilengkapi dengan mekanisme pengecekan integritas data menggunakan algoritma hashing. Jika ada data yang diubah atau dimanipulasi selama proses pengiriman, hal tersebut dapat terdeteksi dan koneksi bisa langsung diputus demi keamanan.
- **Verifikasi Identitas Server:** Saat menggunakan Telnet, client tidak memiliki cara untuk memastikan bahwa server yang dihubungi benar-benar server yang sah. Kondisi ini membuat Telnet rentan terhadap serangan seperti spoofing, di mana penyerang menyamar sebagai server tujuan. SSH menggunakan sistem host key untuk memverifikasi identitas server setiap kali koneksi dibuat. Jika host key berubah secara mencurigakan, SSH akan memberi peringatan kepada pengguna. Dengan begitu, potensi serangan penyamaran server bisa dicegah lebih awal.
- **Performa dan Overhead:** Dari segi kecepatan koneksi murni, Telnet sedikit lebih ringan karena tidak melakukan proses enkripsi/dekripsi. Secara teori, Telnet sedikit lebih cepat dan menggunakan resource lebih rendah. SSH memiliki overhead tambahan karena proses enkripsi, pertukaran kunci (key exchange), dan verifikasi integritas data. Namun, perbedaan performa ini biasanya tidak terlalu signifikan pada penggunaan modern, dan jauh sebanding dengan manfaat keamanan yang didapat.
- **Fitur Tambahan:** Telnet hanya berfungsi sebagai media akses command-line jarak jauh dan tidak memiliki fitur tambahan lain. SSH mendukung berbagai fitur tambahan yang lebih lengkap. Contohnya adalah transfer file secara aman melalui SCP (Secure Copy Protocol) dan SFTP (SSH File Transfer Protocol), port forwarding/tunneling untuk mengamankan aplikasi lain, serta kompresi data yang bisa mempercepat transfer pada koneksi lambat.
- **Penggunaan pada Perangkat Jaringan (Cisco):** Pada perangkat jaringan seperti router dan switch Cisco, Telnet umumnya diaktifkan menggunakan perintah dasar seperti line vty 0 4 dan password, tanpa memerlukan konfigurasi tambahan yang rumit. SSH memerlukan konfigurasi tambahan yang lebih kompleks. Konfigurasi ini meliputi pengaturan hostname, domain-name, generate RSA key (crypto key generate rsa), serta pengaktifan transport input ssh. Meski lebih rumit, hasilnya adalah akses remote yang jauh lebih aman untuk mengelola perangkat jaringan tersebut.

# Access Control List (ACL)

### 1. Pengertian ACL

Access Control List (ACL) adalah kumpulan aturan berurutan (*sequential statements*) yang dikonfigurasi pada router atau switch Layer 3 untuk mengizinkan (*permit*) atau menolak (*deny*) paket data yang melewati sebuah interface, berdasarkan kriteria seperti alamat IP sumber/tujuan, jenis protokol (TCP, UDP, ICMP, dan lain-lain), serta nomor port.

ACL bekerja layaknya seorang penjaga gerbang: setiap paket yang melewati interface yang dipasangi ACL akan dicocokkan dengan daftar aturan yang telah dibuat, kemudian diizinkan atau ditolak berdasarkan aturan yang paling pertama cocok.

#### Skenario yang dipakai pada seluruh contoh ACL

Melanjutkan topologi Inter-VLAN Routing di atas: VLAN 10 (Finance) dan VLAN 20 (Sales) sudah saling terhubung lewat RoAS/SVI. Sekarang tambahkan satu VLAN lagi, **VLAN 30 (IT, 192.168.30.0/24, gateway 192.168.30.1)**, pada router/switch yang sama.

Setelah ketiganya di-routing, ketiga VLAN sebenarnya sudah bisa saling terhubung penuh. Namun kebijakan perusahaan menyatakan:

| Pasangan VLAN | Kebijakan |
|---|---|
| VLAN 10 ↔ VLAN 20 | ✅ Boleh |
| VLAN 20 ↔ VLAN 30 | ✅ Boleh |
| VLAN 10 ↔ VLAN 30 | ❌ Diblokir, **kedua arah** |

Jadi bukan blokir total ke satu tujuan, melainkan pembatasan selektif antar-pasangan VLAN. Untuk melengkapi skenario, tambahkan konfigurasi VLAN 30 berikut:

```
! ===== Switch: tambah VLAN 30 =====
SW1(config)# vlan 30
SW1(config-vlan)# name IT
SW1(config-vlan)# exit
SW1(config)# interface fastEthernet 0/4
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 30
SW1(config-if)# exit
SW1(config)# interface fastEthernet 0/3
SW1(config-if)# switchport trunk allowed vlan 10,20,30
SW1(config-if)# exit

! ===== Router (RoAS): tambah sub-interface VLAN 30 =====
R1(config)# interface FastEthernet 0/0.30
R1(config-subif)# encapsulation dot1Q 30
R1(config-subif)# ip address 192.168.30.1 255.255.255.0
R1(config-subif)# exit
```

| Perangkat | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC1 (VLAN 10) | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 (VLAN 20) | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC3 (VLAN 30) | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |

> ⚠️ Pastikan ping antar ketiga VLAN sudah **berhasil semua** sebelum memasang ACL. Kalau ACL dipasang saat routing belum beres, kamu tidak bisa membedakan penyebab gagalnya: routing atau ACL.

**Fungsi dan manfaat ACL:**
- **Keamanan jaringan** — membatasi akses ke perangkat atau subnet tertentu.
- **Kontrol trafik** — membatasi jenis trafik tertentu untuk menghemat bandwidth.
- **Filtering rute** — memfilter update routing bersama fitur `distribute-list`.
- **QoS** — mengklasifikasikan trafik untuk diprioritaskan.
- **NAT & VPN** — menentukan trafik mana yang perlu di-translate atau dienkripsi.

### 2. Cara Kerja ACL

Setiap ACL terdiri atas satu atau lebih *access control entries* (ACE) yang diproses secara berurutan dari atas ke bawah (*top-down*). Alurnya sebagai berikut:

1. Paket dibandingkan dengan ACE pertama pada ACL.
2. Jika kondisi cocok, aksi *permit* atau *deny* langsung dijalankan; proses pencocokan berhenti.
3. Jika tidak cocok, paket dibandingkan dengan ACE berikutnya, dan seterusnya hingga akhir daftar.
4. Jika paket tidak cocok dengan ACE mana pun, paket tersebut akan ditolak secara otomatis oleh aturan *implicit deny* yang tersembunyi di akhir setiap ACL.

> ⚠️ **Implicit Deny:** Setiap ACL Cisco selalu diakhiri dengan aturan `deny any` yang tidak terlihat pada konfigurasi. Artinya, jika sebuah ACL hanya berisi aturan *deny* tanpa satu pun *permit*, atau berisi *permit* yang tidak mencakup semua trafik yang diinginkan, maka seluruh trafik yang tidak cocok akan otomatis ditolak. Karena itu, ACL yang tujuannya "blokir sebagian saja" **wajib diakhiri** dengan `permit any` (Standard) atau `permit ip any any` (Extended).

**Contoh alur (pakai ACL 110 di Bab 3.2):** paket dari PC1 (192.168.10.10) menuju PC3 (192.168.30.10).
```
Baris 10: deny ip 192.168.10.0/24 -> 192.168.30.0/24   ← COCOK, paket DITOLAK. Selesai.
```
Paket dari PC1 menuju PC2 (192.168.20.10):
```
Baris 10: deny ip 192.168.10.0/24 -> 192.168.30.0/24   ← tujuan bukan VLAN 30, tidak cocok
Baris 20: deny ip 192.168.30.0/24 -> 192.168.10.0/24   ← sumber bukan VLAN 30, tidak cocok
Baris 30: permit ip any any                            ← COCOK, paket DIIZINKAN.
```

**Wildcard Mask:** ACL Cisco tidak menggunakan subnet mask biasa, melainkan *wildcard mask* untuk menentukan rentang IP yang dicocokkan. Prinsipnya berkebalikan dengan subnet mask: bit `0` berarti oktet harus cocok persis, bit `1` berarti oktet boleh bernilai apa saja (diabaikan).

| Kebutuhan | Subnet Mask | Wildcard Mask |
|---|---|---|
| Host tunggal (192.168.10.5) | 255.255.255.255 | 0.0.0.0 |
| Subnet /24 (192.168.10.0) | 255.255.255.0 | 0.0.0.255 |
| Subnet /16 (172.16.0.0) | 255.255.0.0 | 0.0.255.255 |
| Semua alamat (any) | 0.0.0.0 | 255.255.255.255 |

Cara cepat menghitung wildcard mask: kurangi setiap oktet subnet mask dari 255. Contoh: `255.255.255.192` → wildcard `0.0.0.63` (255−192=63). Kata kunci `any` adalah singkatan dari `0.0.0.0 255.255.255.255`, sedangkan `host <ip>` adalah singkatan dari `<ip> 0.0.0.0`.

Jadi `192.168.10.0 0.0.0.255` dibaca: "tiga oktet pertama harus 192.168.10, oktet terakhir bebas" — yaitu seluruh VLAN 10.

### 3. Jenis-Jenis ACL

ACL Cisco dibedakan dengan **dua cara** yang saling bersilangan:

1. **Berdasarkan kriteria filter:** *Standard* (hanya IP sumber) dan *Extended* (IP sumber, IP tujuan, protokol, port).
2. **Berdasarkan cara identifikasi:** *Numbered* (memakai nomor) dan *Named* (memakai nama).

Keduanya digabung sehingga ada **empat kombinasi**: Standard Numbered, Extended Numbered, Standard Named, dan Extended Named.

| | Standard | Extended |
|---|---|---|
| **Numbered** | Nomor 1–99 / 1300–1999 | Nomor 100–199 / 2000–2699 |
| **Named** | `ip access-list standard <nama>` | `ip access-list extended <nama>` |

> 💡 **Ringkasnya:** *Standard vs Extended* menjawab pertanyaan "**apa** yang bisa disaring?". *Numbered vs Named* menjawab pertanyaan "**bagaimana** ACL itu diberi identitas?". Fungsi penyaringannya sama; yang beda hanya cara menulis dan mengelolanya.

#### 3.1 Numbered ACL

Numbered ACL adalah ACL yang diidentifikasi dengan **nomor**. Rentang nomor menentukan jenisnya, sehingga tidak perlu menulis kata `standard` atau `extended` saat membuatnya.

**Karakteristik Numbered ACL:**
- Dibuat langsung dari mode global configuration dengan perintah `access-list <nomor> ...`.
- Nomor menentukan jenis: 1–99 atau 1300–1999 adalah Standard, 100–199 atau 2000–2699 adalah Extended.
- Setiap baris dimasukkan satu per satu dengan nomor ACL yang sama.
- Dengan cara `access-list` biasa, satu baris tidak bisa dihapus tersendiri. Menghapus dengan `no access-list <nomor>` akan menghapus **seluruh** ACL.
- Kurang deskriptif karena hanya berupa angka, sehingga sulit dipahami pada konfigurasi besar.

> ⚠️ **Jangan tertukar:** nomor ACL (misal `access-list 10`) **tidak ada hubungannya** dengan VLAN ID. Nomor 10 di sini hanya penanda ACL nomor 10. Pada contoh di bawah, sengaja dipakai nomor 11 dan 12 agar tidak tertukar dengan VLAN 10, 20, dan 30.

##### 3.1.1 Standard Numbered ACL

Menyaring paket hanya berdasarkan **IP sumber**. Sesuai aturan penempatan, dipasang **dekat tujuan**.

**Sintaks:**
```
access-list <1-99> {permit | deny} <ip-sumber> <wildcard>
```

Karena Standard ACL tidak mengenali IP tujuan, satu ACL hanya bisa menutup **satu arah**. Untuk skenario "blokir VLAN 10 ↔ VLAN 30", dibutuhkan **dua ACL terpisah**.

```
! ===== ACL 11: blokir trafik DARI VLAN 10 yang menuju VLAN 30 =====
R1> enable
R1# configure terminal
R1(config)# access-list 11 deny 192.168.10.0 0.0.0.255
R1(config)# access-list 11 permit any

! Pasang dekat TUJUAN (sub-interface VLAN 30), arah keluar (out)
R1(config)# interface FastEthernet 0/0.30
R1(config-subif)# ip access-group 11 out
R1(config-subif)# exit

! ===== ACL 12: blokir trafik DARI VLAN 30 yang menuju VLAN 10 =====
R1(config)# access-list 12 deny 192.168.30.0 0.0.0.255
R1(config)# access-list 12 permit any

! Pasang dekat TUJUAN (sub-interface VLAN 10), arah keluar (out)
R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# ip access-group 12 out
R1(config-subif)# exit
```

**Penjelasan:**
- ACL 11 dipasang di `Fa0/0.30` arah **out**. Semua paket yang akan keluar ke VLAN 30 diperiksa; jika sumbernya VLAN 10, dibuang. Sumber lain (VLAN 20) lolos lewat `permit any`.
- ACL 12 dipasang di `Fa0/0.10` arah **out** untuk menutup arah sebaliknya.
- Baris `permit any` wajib ada; tanpanya, implicit deny akan memblokir **semua** trafik, termasuk VLAN 20.
- **Keterbatasan:** Standard ACL memblokir *seluruh* trafik dari sumber tersebut ke interface itu, dan tidak bisa memilih protokol atau port tertentu.

##### 3.1.2 Extended Numbered ACL

Menyaring berdasarkan **IP sumber, IP tujuan, protokol, dan port**. Dipasang **dekat sumber**.

**Sintaks:**
```
access-list <100-199> {permit | deny} <protokol> <sumber> <wildcard> <tujuan> <wildcard> [eq <port>]
```

```
! ===== 1. Membuat ACL =====
R1> enable
R1# configure terminal
R1(config)# access-list 110 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
R1(config)# access-list 110 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
R1(config)# access-list 110 permit ip any any

! ===== 2. Terapkan di dekat SUMBER (inbound) =====
R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# ip access-group 110 in
R1(config-subif)# exit

R1(config)# interface FastEthernet 0/0.30
R1(config-subif)# ip access-group 110 in
R1(config-subif)# exit
```

**Penjelasan logika konfigurasi:**
- **Baris `deny` pertama** menolak paket dari VLAN 10 menuju VLAN 30.
- **Baris `deny` kedua** menolak paket dari VLAN 30 menuju VLAN 10 — wajib ada karena ACL memproses satu arah per paket; tanpa baris ini, arah sebaliknya tetap lolos.
- **Baris `permit ip any any`** wajib ada supaya trafik lain — termasuk VLAN 10 ↔ VLAN 20 dan VLAN 20 ↔ VLAN 30 — tetap diizinkan (tanpa ini, semua trafik lain ikut ter-*block* oleh *implicit deny*).
- ACL yang sama diterapkan di **dua sub-interface** (VLAN 10 dan VLAN 30, arah inbound) karena ACL hanya memeriksa paket yang lewat di interface tempat ia dipasang.
- ACL **tidak perlu dipasang di sub-interface VLAN 20**, karena tidak ada kriteria `deny` yang menyebut VLAN 20.

**Contoh tambahan — filter berdasarkan protokol/port:** VLAN 10 dilarang mengakses web server (HTTP, port 80) di VLAN 30, tetapi trafik lain tetap boleh.
```
R1(config)# access-list 120 deny tcp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 eq 80
R1(config)# access-list 120 permit ip any any
R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# ip access-group 120 in
```
Contoh ini **tidak bisa** dibuat dengan Standard ACL karena membutuhkan IP tujuan dan nomor port. Ping dari VLAN 10 ke VLAN 30 tetap berhasil, hanya akses web (HTTP) yang ditolak.

**Verifikasi:**
```
R1# show access-lists 110
Extended IP access list 110
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
    20 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
    30 permit ip any any
```

#### 3.2 Named ACL

Named ACL adalah ACL yang diidentifikasi dengan **nama** deskriptif, bukan nomor. Kriteria filternya tetap sama dengan Standard atau Extended, sehingga jenisnya harus dideklarasikan saat pembuatan.

**Karakteristik Named ACL:**
- Dibuat dengan `ip access-list {standard | extended} <nama>`, lalu masuk ke mode konfigurasi ACL (`config-std-nacl` atau `config-ext-nacl`).
- Nama bersifat deskriptif (misal `BLOCK_V10_TO_V30`), sehingga mudah dibaca dan dikelola.
- Mendukung penyisipan dan penghapusan satu baris (ACE) memakai nomor *sequence*, tanpa menghapus seluruh ACL.
- Di dalam mode ACL, perintah **tidak lagi diawali kata `access-list`**; langsung `permit` atau `deny`.
- Nama bersifat *case-sensitive* (`BLOCK` berbeda dengan `block`) dan tidak boleh mengandung spasi.

##### 3.2.1 Standard Named ACL

Fungsinya sama dengan Standard Numbered (hanya IP sumber, dipasang dekat tujuan), tetapi memakai nama.

```
! ===== ACL 1: blokir trafik DARI VLAN 10 yang menuju VLAN 30 =====
R1> enable
R1# configure terminal
R1(config)# ip access-list standard BLOCK_V10_TO_V30
R1(config-std-nacl)# deny 192.168.10.0 0.0.0.255
R1(config-std-nacl)# permit any
R1(config-std-nacl)# exit

R1(config)# interface FastEthernet 0/0.30
R1(config-subif)# ip access-group BLOCK_V10_TO_V30 out
R1(config-subif)# exit

! ===== ACL 2: blokir trafik DARI VLAN 30 yang menuju VLAN 10 =====
R1(config)# ip access-list standard BLOCK_V30_TO_V10
R1(config-std-nacl)# deny 192.168.30.0 0.0.0.255
R1(config-std-nacl)# permit any
R1(config-std-nacl)# exit

R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# ip access-group BLOCK_V30_TO_V10 out
R1(config-subif)# exit
```

Sama seperti Standard Numbered, dibutuhkan dua ACL karena hanya satu arah yang bisa ditutup per ACL. Bedanya hanya pada cara penulisan: nama menggantikan nomor, dan perintah di dalam ACL tidak diawali `access-list`.

##### 3.2.2 Extended Named ACL

Fungsinya sama dengan Extended Numbered (IP sumber, tujuan, protokol, port; dipasang dekat sumber), tetapi memakai nama.

```
! ===== 1. Membuat Named ACL =====
R1> enable
R1# configure terminal
R1(config)# ip access-list extended BLOCK_FINANCE_IT
R1(config-ext-nacl)# deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
R1(config-ext-nacl)# deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
R1(config-ext-nacl)# permit ip any any
R1(config-ext-nacl)# exit

! ===== 2. Terapkan di kedua sub-interface (inbound) =====
R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# ip access-group BLOCK_FINANCE_IT in
R1(config-subif)# exit

R1(config)# interface FastEthernet 0/0.30
R1(config-subif)# ip access-group BLOCK_FINANCE_IT in
R1(config-subif)# exit
```

**Menyisipkan atau menghapus baris tertentu** (keunggulan Named ACL). Setiap baris otomatis diberi nomor *sequence* (10, 20, 30, ...). Kita bisa menyisipkan baris baru dengan nomor di antaranya.

Contoh: perusahaan ingin VLAN 10 dan VLAN 30 tetap bisa saling mengirim email (SMTP, port 25), meski trafik lain tetap diblokir.

```
R1(config)# ip access-list extended BLOCK_FINANCE_IT

! Sequence 5 dan 6 lebih kecil dari 10, jadi diperiksa SEBELUM aturan deny
! Seq 5: VLAN 10 (klien) -> server SMTP di VLAN 30, port tujuan 25
R1(config-ext-nacl)# 5 permit tcp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 eq 25

! Seq 6: balasan dari VLAN 30 (port sumber 25) kembali ke VLAN 10
R1(config-ext-nacl)# 6 permit tcp 192.168.30.0 0.0.0.255 eq 25 192.168.10.0 0.0.0.255
R1(config-ext-nacl)# exit
```

> ⚠️ **Kenapa butuh dua baris?** Koneksi TCP itu dua arah. Klien mengirim ke port 25 (baris 5), lalu server membalas **dari port 25** ke klien (baris 6). Kalau hanya baris 5 yang ada, balasan server terkena `deny` kedua dan koneksi email tidak pernah selesai.

Hasil `show access-lists BLOCK_FINANCE_IT` setelah penyisipan:
```
Extended IP access list BLOCK_FINANCE_IT
    5 permit tcp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 eq smtp
    6 permit tcp 192.168.30.0 0.0.0.255 eq smtp 192.168.10.0 0.0.0.255
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
    20 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
    30 permit ip any any
```

Menghapus satu baris saja (misal pengecualian sequence 6), tanpa menghapus ACL lain:
```
R1(config)# ip access-list extended BLOCK_FINANCE_IT
R1(config-ext-nacl)# no 6
R1(config-ext-nacl)# exit
```

> 💡 **Catatan:** Pada IOS modern, Numbered ACL juga bisa diedit per baris dengan masuk lewat `ip access-list extended 110`. Perbedaan utama Numbered dan Named ada pada cara pembuatan dan keterbacaan, bukan lagi soal kemampuan edit. Namun cara lama `access-list 110 ...` tetap tidak bisa menghapus satu baris saja.

#### 3.3 Perbandingan Jenis ACL

| Aspek | Standard Numbered | Extended Numbered | Standard Named | Extended Named |
|---|---|---|---|---|
| Kriteria filter | IP sumber | IP sumber, tujuan, protokol, port | IP sumber | IP sumber, tujuan, protokol, port |
| Identifikasi | Nomor 1–99 / 1300–1999 | Nomor 100–199 / 2000–2699 | Nama | Nama |
| Perintah pembuatan | `access-list 11 ...` | `access-list 110 ...` | `ip access-list standard <nama>` | `ip access-list extended <nama>` |
| Penempatan ideal | Dekat tujuan | Dekat sumber | Dekat tujuan | Dekat sumber |
| Edit per baris | Terbatas (cara lama tidak bisa) | Terbatas (cara lama tidak bisa) | Ya (sequence number) | Ya (sequence number) |
| Blokir pasangan VLAN dua arah | ❌ Butuh 2 ACL, tidak kenal tujuan | ✅ 1 ACL, dipasang di 2 interface | ❌ Butuh 2 ACL, tidak kenal tujuan | ✅ 1 ACL, mudah dikelola |

### 4. Aturan Penempatan ACL

**Arah inbound dan outbound** selalu dilihat dari sudut pandang **router/interface**:
- **Inbound (`in`)** — paket diperiksa ACL saat **masuk** ke interface, sebelum diproses tabel routing. Lebih efisien jika paket memang akan ditolak.
- **Outbound (`out`)** — paket diproses routing dulu, baru diperiksa ACL saat akan **keluar** dari interface.

**Aturan lokasi:**
- **Standard ACL** → letakkan dekat **tujuan**, karena tidak mengenali IP tujuan. Jika diletakkan dekat sumber, ia akan memblokir sumber itu ke *semua* tujuan, termasuk yang seharusnya masih boleh.
- **Extended ACL** → letakkan dekat **sumber**, karena sudah mengenali IP tujuan dan protokol, sehingga paket yang seharusnya ditolak bisa langsung dibuang lebih awal dan tidak membebani jaringan.
- **Pembatasan dua arah antar dua VLAN spesifik** (seperti VLAN 10 ↔ VLAN 30) butuh ACL yang sama dipasang di **kedua sisi** (kedua sub-interface/SVI yang terlibat), arah inbound, karena masing-masing sisi hanya melihat trafik yang masuk dari VLAN-nya sendiri.
- Setiap interface hanya boleh punya maksimal **satu ACL per arah, per protokol**.
- ACL **hanya menyaring trafik yang melewati router**. Trafik yang dibuat oleh router itu sendiri tidak terkena ACL yang dipasang pada interfacenya.

**Menerapkan ACL pada metode SVI:** jika memakai switch Layer 3, ACL dipasang pada interface SVI, bukan sub-interface. Isi ACL-nya sama persis.
```
SW1(config)# interface vlan 10
SW1(config-if)# ip access-group 110 in
SW1(config-if)# exit
SW1(config)# interface vlan 30
SW1(config-if)# ip access-group 110 in
SW1(config-if)# exit
```

### 5. Verifikasi ACL

| Perintah | Fungsi |
|---|---|
| `show access-lists` | Menampilkan semua ACL beserta jumlah paket yang cocok (*match*) tiap baris |
| `show access-lists <nomor/nama>` | Menampilkan detail satu ACL tertentu |
| `show ip interface <interface>` | Menampilkan ACL yang diterapkan pada suatu interface beserta arahnya |
| `show running-config` | Menampilkan konfigurasi ACL secara lengkap sesuai urutan baris |
| `ping` / `traceroute` | Menguji apakah trafik benar-benar diizinkan atau ditolak sesuai ACL |

**Uji Verifikasi (sesuai skenario VLAN 10/20/30):** lakukan ping dari **Command Prompt masing-masing PC** di Packet Tracer.

```
PC1> ping 192.168.20.10    ! HARUS berhasil (VLAN 10 -> VLAN 20)
PC2> ping 192.168.30.10    ! HARUS berhasil (VLAN 20 -> VLAN 30)
PC1> ping 192.168.30.10    ! HARUS gagal (VLAN 10 -> VLAN 30)
PC3> ping 192.168.10.10    ! HARUS gagal (VLAN 30 -> VLAN 10)
```

> 💡 **Kenapa ping dari PC, bukan dari router?** Perintah `ping <tujuan> source <ip>` di router hanya bisa memakai IP milik **router itu sendiri** sebagai sumber (misal `source 192.168.10.1`), bukan IP milik PC. Untuk menguji ACL seperti kondisi sebenarnya, ping langsung dari PC.

Hasil gagal biasanya tampil sebagai `Request timed out` atau `Destination host unreachable`. Keduanya menandakan paket tidak sampai.

Setelah ping, cek jumlah paket yang cocok pada tiap baris:
```
R1# show access-lists 110
Extended IP access list 110
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 (4 match(es))
    20 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255 (4 match(es))
    30 permit ip any any (16 match(es))
```
Angka *match(es)* naik setiap ada paket yang cocok, sehingga bisa dipakai untuk memastikan baris mana yang bekerja.

**Masalah umum:**

| Gejala | Penyebab | Solusi |
|---|---|---|
| Semua trafik terblokir | Lupa `permit any` di akhir ACL | Tambahkan baris permit eksplisit sebelum implicit deny |
| ACL tidak berefek | Belum ada `ip access-group` di interface | Terapkan ACL pada interface dengan arah in/out yang benar |
| ACL tidak berefek (meski sudah dipasang) | Salah arah (`in` tertukar `out`) atau salah interface | Cek dengan `show ip interface <interface>` |
| VLAN 30 → VLAN 10 masih lolos padahal VLAN 10 → VLAN 30 sudah diblokir | Hanya satu baris `deny` (satu arah) yang dibuat | Tambahkan baris `deny` untuk arah sebaliknya |
| Trafik yang seharusnya diizinkan malah ditolak | Urutan ACE salah | Susun ulang dari yang paling spesifik ke paling umum |
| Trafik sumber lain ikut terblokir | Standard ACL diterapkan terlalu dekat sumber | Pindahkan ke interface dekat destination, atau ganti ke Extended ACL |
| Error `% Invalid input` saat membuat ACL | Nomor tidak sesuai jenis (misal `permit ip` pada nomor 10) | Standard (1–99) hanya menerima IP sumber; pakai 100–199 untuk Extended |
| Email/TCP tidak jalan padahal sudah di-permit | Hanya satu arah yang di-permit | Tambahkan permit untuk balasan (port sumber = port layanan) |



### 4. Latihan Praktikum
1. Buat topologi sederhana di Cisco Packet Tracer menggunakan **1 Router** dan **1 PC** (hubungkan langsung menggunakan kabel console)!
2. Konfigurasikan **password pada line console 0** dengan password bebas (contoh: `12345`), lalu aktifkan `login` supaya password diminta saat login!
3. Screenshot hasil uji verifikasi (saat membuka kembali akses ke Router, harus muncul prompt `Password:`)!

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- cisco book - taufik
