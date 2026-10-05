# WLAN, Keamanan CLI, Remote Access, dan ACL

### Topologi yang Digunakan

Praktikum ini memakai **Cisco Packet Tracer** dan terdiri dari tiga bagian topologi yang saling berkaitan:

- **Topologi WLAN**: satu **Access Point**, satu **Smartphone**, dan satu **Laptop**. Client terhubung tanpa kabel ke Access Point yang diamankan dengan **WPA2-PSK**.
- **Topologi Remote Access**: **Router**, **Switch**, dan beberapa PC. Ada dua segmen LAN:
  - **192.168.10.0/24** (Smartphone0, Laptop0, PC0, PC1), gateway `192.168.10.1`
  - **192.168.20.0/24** (PC2), gateway `192.168.20.1`
  - Switch dikelola lewat **Telnet** (`192.168.10.254`) dan Router dikelola lewat **SSH** (`192.168.10.1`).
- **Topologi ACL (Inter-VLAN Routing)**: **Switch SW1**, **Router R1 (RoAS)**, dan tiga PC di tiga VLAN:
  - **VLAN 10 (Finance)**, `192.168.10.0/24`, gateway `192.168.10.1`
  - **VLAN 20 (Sales)**, `192.168.20.0/24`, gateway `192.168.20.1`
  - **VLAN 30 (IT)**, `192.168.30.0/24`, gateway `192.168.30.1`

Alur besarnya: perangkat terhubung ke jaringan (kabel atau WLAN), akses ke perangkat jaringan diamankan dengan password (console, enable secret), perangkat dikelola dari jauh lewat Telnet atau SSH, lalu lalu lintas antar-VLAN dibatasi dengan ACL.

**Tabel IP topologi Remote Access:**

| Device      | IP Address   | Subnet Mask   | Default Gateway |
| ----------- | ------------ | ------------- | --------------- |
| Smartphone0 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1    |
| Laptop0     | 192.168.10.3 | 255.255.255.0 | 192.168.10.1    |
| PC0         | 192.168.10.4 | 255.255.255.0 | 192.168.10.1    |
| PC1         | 192.168.10.5 | 255.255.255.0 | 192.168.10.1    |
| PC2         | 192.168.20.2 | 255.255.255.0 | 192.168.20.1    |

**Tabel IP topologi ACL:**

| Perangkat     | IP Address    | Subnet Mask   | Default Gateway |
| ------------- | ------------- | ------------- | --------------- |
| PC1 (VLAN 10) | 192.168.10.10 | 255.255.255.0 | 192.168.10.1    |
| PC2 (VLAN 20) | 192.168.20.10 | 255.255.255.0 | 192.168.20.1    |
| PC3 (VLAN 30) | 192.168.30.10 | 255.255.255.0 | 192.168.30.1    |

### Tujuan Pembelajaran

- Memahami konsep WLAN dan cara kerja Access Point.
- Mengonfigurasi Access Point (SSID dan WPA2-PSK) serta menghubungkan client.
- Memastikan client mendapat IP otomatis lewat DHCP.
- Mengamankan akses console dan Privilege Mode pada Cisco Router.
- Mengonfigurasi remote access dengan Telnet dan SSH, serta membedakan keduanya.
- Memahami cara kerja, jenis, dan penempatan ACL.
- Membuat ACL Standard, Extended, Numbered, dan Named untuk membatasi trafik antar-VLAN.

---

## Bagian 1: WLAN (Wireless Local Area Network)

WLAN adalah jaringan komputer lokal (gedung, kampus, rumah) yang menghubungkan perangkat **tanpa kabel fisik**. WLAN memakai gelombang radio pada pita **2,4 GHz** dan **5 GHz** berdasarkan standar **IEEE 802.11** (secara komersial disebut Wi-Fi).

Pusat dari WLAN adalah **Access Point (AP)** atau wireless router. Perangkat ini berfungsi sebagai pemancar dan penerima (transceiver) yang menjembatani perangkat nirkabel (laptop, tablet, ponsel) dengan jaringan kabel atau ISP. Selama pengguna masih berada di area cakupan AP, koneksi tetap terjaga otomatis.

**Kapan cocok untuk dipakai?**

- Ruangan atau area yang sulit ditarik kabel
- Pengguna yang sering berpindah tempat (laptop, HP, tamu)
- Jaringan yang perlu mudah dikembangkan

##### Karakteristik

- **Medium udara** - data dikirim lewat frekuensi radio, bukan kabel
- **Access Point sebagai pusat** - semua client wireless terhubung lewat AP
- **SSID** - nama jaringan yang tampil saat client mencari Wi-Fi
- **Default terbuka** - secara bawaan AP tidak memiliki keamanan, jadi wajib diatur
- **WPA2-PSK** - jenis enkripsi dengan satu password bersama (Pre-Shared Key)
- **Client butuh modul wireless** - smartphone sudah punya bawaan, laptop perlu modul **WPC300N**

##### Kelebihan

- **Mobilitas tinggi** - pengguna bebas berpindah dalam area cakupan
- **Mudah dikembangkan** - menambah client tidak perlu menarik kabel baru
- **Fleksibel** - cocok untuk perangkat yang tidak punya port LAN

##### Kekurangan

- **Rentan disadap** jika tidak diamankan, karena sinyal menyebar di udara
- **Dibatasi jangkauan** - kualitas sinyal turun jika jauh dari AP
- **Bisa terganggu** oleh interferensi dan penghalang

##### Konfigurasi

###### Access Point

Secara default jaringan AP terbuka tanpa keamanan. Atur SSID, enkripsi, dan password:

1. Klik **Access Point**, buka tab **Config**, pilih **Port 1** (Wireless).
2. Isi **SSID** dengan nama jaringan, misalnya `[nama SSID]`.
3. Pada **Authentication**, pilih **WPA2-PSK**.
4. Isi **PSK Pass Phrase** dengan password, lalu pastikan **Encryption Type** adalah **AES**.

###### Smartphone

1. Klik **Smartphone**, buka tab **Config**, pilih interface **Wireless0**.
2. Isi **SSID** sesuai AP.
3. Pilih **WPA2-PSK** dan isi password yang sama dengan AP.

###### Laptop

Laptop belum memiliki modul wireless, jadi modul fisiknya harus diganti dulu:

1. Klik **Laptop**, buka tab **Physical**.
2. **Matikan daya laptop** (klik tombol power).
3. Lepaskan modul LAN bawaan, ganti dengan modul wireless **WPC300N**.
4. Nyalakan kembali laptop.
5. Lanjutkan konfigurasi seperti pada Smartphone (tab **Config**, interface **Wireless0**, SSID, WPA2-PSK, password).

**Uji:** Pastikan client terhubung ke AP dan mendapat IP otomatis dari DHCP (cek di tab **Desktop > IP Configuration** atau jalankan `ipconfig`). Setelah itu coba `ping` ke perangkat lain di jaringan.

---

## Bagian 2: Keamanan CLI (Command Line Interface)

Konsep ini mengamankan akses ke router atau switch Cisco lewat CLI, supaya tidak sembarang orang bisa masuk dan mengubah konfigurasi perangkat.

**Kapan cocok untuk dipakai?**

- Setiap perangkat jaringan yang dipasang di lingkungan nyata
- Perangkat yang bisa diakses fisik oleh banyak orang
- Sebagai langkah dasar sebelum mengaktifkan remote access

##### Karakteristik

- **Berlapis** - console, privilege mode, dan remote access diamankan secara terpisah
- **`login` wajib ada** - password pada line baru diminta jika perintah `login` aktif
- **`enable secret` terenkripsi** - password tersimpan dalam bentuk hash, berbeda dengan password line yang tampil biasa
- **Hostname** - nama perangkat untuk mempermudah identifikasi

### A. Pengamanan Konsol Fisik (`line console 0`)

Mencegah orang yang punya akses langsung ke port Console masuk dan mengonfigurasi perangkat tanpa izin. Setiap kali perangkat diakses lewat console, password akan diminta.

```
Router>en
Router#conf t
Router(config)#line console 0
Router(config-line)#password 12345
Router(config-line)#login
Router(config-line)#exit
Router(config)#
```

**Uji:** Saat perangkat dibuka kembali, password harus diminta.

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

| Perintah                                  | Fungsi                                                                                        |
| ----------------------------------------- | --------------------------------------------------------------------------------------------- |
| `line console 0`                          | Masuk ke mode konfigurasi line console                                                        |
| `password 12345`                          | Menentukan password yang diminta saat login                                                   |
| `login`                                   | **Wajib ada**, mengaktifkan pengecekan password. Tanpa `login`, password tidak pernah diminta |
| `exit`                                    | Keluar dari mode line console                                                                 |
| `show running-config \| section line con` | Verifikasi bagian line console                                                                |

### B. Pengamanan Privilege Mode (`enable secret`)

Privilege Mode (prompt `Router#`) adalah mode dengan hak akses tinggi untuk perintah administrasi dan konfigurasi, sehingga perlu diamankan.

```
Router>en
Router#conf t
Router(config)#enable secret 67890
Router(config)#
```

**Uji:** Saat menjalankan `enable`, router meminta password.

```
Router>en
Password:
Router#show running-config | include enable secret
enable secret 5 $1$mERr$QIs3x0SqQACMwbwSHN8Y7.
Router#
```

| Perintah                                       | Fungsi                                                         |
| ---------------------------------------------- | -------------------------------------------------------------- |
| `enable secret 67890`                          | Menentukan password untuk masuk ke Privilege EXEC Mode         |
| `enable`                                       | Masuk dari User EXEC (`Router>`) ke Privilege EXEC (`Router#`) |
| `show running-config \| include enable secret` | Verifikasi konfigurasi `enable secret`                         |
| `no enable secret`                             | Menghapus konfigurasi `enable secret`                          |

### C. Konfigurasi Hostname

Mengubah nama perangkat agar mudah dikenali. Secara default namanya `Router` atau `Switch`.

```
Router#conf t
Router(config)#hostname roni
roni(config)#ex
roni#
```

**Uji:** Prompt berubah dari `Router#` menjadi `roni#`.

```
roni#show running-config | include hostname
hostname roni
roni#
```

| Perintah                                  | Fungsi                                     |
| ----------------------------------------- | ------------------------------------------ |
| `hostname roni`                           | Mengubah nama perangkat menjadi `roni`     |
| `show running-config \| include hostname` | Verifikasi hostname                        |
| `no hostname`                             | Mengembalikan hostname ke default `Router` |

---

## Bagian 3: Remote Access (Telnet dan SSH)

Remote access memungkinkan administrator mengakses dan mengonfigurasi router atau switch dari jarak jauh lewat jaringan, tanpa harus berada di depan perangkat.

### A. Telnet

Telnet (Telecommunication Network) adalah protokol tertua untuk remote login, dipakai sejak 1969 dan berjalan pada **port 23**. Kelemahan utamanya ada pada keamanan: seluruh data, termasuk username dan password, dikirim sebagai **plain text tanpa enkripsi**, sehingga mudah disadap (sniffing), misalnya dengan Wireshark.

**Kapan cocok untuk dipakai?**

- Hanya untuk lab atau praktikum
- Jaringan tertutup yang benar-benar terpercaya

##### Karakteristik

- **Port 23** - port default Telnet
- **Plain text** - username dan password terlihat jelas saat dikirim
- **Konfigurasi sederhana** - cukup `line vty` dan `password`
- **`transport input telnet`** - membatasi VTY hanya menerima Telnet

##### Kelebihan

- **Konfigurasi mudah** - tidak butuh RSA key atau domain name
- **Ringan** - tanpa proses enkripsi, resource lebih kecil

##### Kekurangan

- **Tidak aman** - data dan password bisa disadap
- **Tidak ada verifikasi integritas** - data bisa diubah di tengah jalan (man-in-the-middle)
- **Tidak ada verifikasi server** - rentan spoofing
- **Jarang dipakai** - sudah digantikan SSH

##### Konfigurasi

###### Router On A Stick untuk vlan 1

```
Router(config)#interface fa0/0.1
Router(config-subif)#encapsulation dot1Q 1
Router(config-subif)#ip address 192.168.1.249 255.255.255.248
Router(config-subif)#exit
```

###### IP Switch (VLAN 1)

```
Switch>enable
Switch#configure terminal
Switch(config)#interface vlan 1
Switch(config-if)#ip address 192.168.1.253 255.255.255.248
Switch(config-if)#no shutdown
Switch(config-if)#exit
Switch(config)ip default-gateway 192.168.1.249 
```

###### Telnet di Switch

```
Switch>enable
Switch#configure terminal
Switch(config)#line vty 0 4
Switch(config-line)#transport input telnet
Switch(config-line)#password 12345
Switch(config-line)#login
Switch(config-line)#exit
Switch(config)#enable secret 67890
Switch(config)#exit
```

> **`line`**: masuk ke mode konfigurasi untuk sebuah "jalur" akses ke perangkat.
> 
> 
> 
> **`vty`**: *Virtual Terminal*, yaitu jalur **virtual** untuk akses lewat jaringan (Telnet atau SSH). Disebut virtual karena tidak ada port fisiknya, berbeda dengan port console yang berupa colokan kabel.
> 
> 
> 
> **`0 4`**: rentang nomor jalur, dari 0 sampai 4. Artinya ada **5 jalur**, jadi switch bisa melayani **5 sesi remote sekaligus**.

**Uji:** Buka Command Prompt di salah satu PC, lalu telnet ke IP switch.

```
C:\>ping 192.168.20.14 (ping ke gateway vlan itu sendiri)
C:\>ping 192.168.1.1 (ping ke gateway vlan 1)
C:\>ping 192.168.1.254 (Tes koneksi ke jaringan)
C:\>telnet 192.168.1.254
Trying 192.168.1.254 ...Open


User Access Verification

Password:
Switch>en
Password:
Switch#
```

| Perintah                   | Fungsi                                                   |
| -------------------------- | -------------------------------------------------------- |
| `enable` / `en`            | Masuk ke privileged EXEC mode                            |
| `configure terminal`       | Masuk ke global configuration mode                       |
| `interface vlan 1`         | Masuk ke konfigurasi interface VLAN 1 (switch)           |
| `ip address <ip> <subnet>` | Memberi alamat IP pada interface                         |
| `no shutdown`              | Mengaktifkan interface                                   |
| `line vty 0 4`             | Masuk ke konfigurasi line VTY 0 sampai 4 (akses remote)  |
| `transport input telnet`   | Mengizinkan hanya Telnet pada line VTY                   |
| `password <password>`      | Mengatur password login VTY                              |
| `login`                    | Mengaktifkan permintaan password saat login              |
| `enable secret <password>` | Mengatur password terenkripsi untuk privileged EXEC mode |
| `exit`                     | Keluar dari mode konfigurasi saat ini                    |
| `telnet <ip>`              | Remote ke perangkat tujuan lewat Telnet                  |

### B. SSH (Secure Shell)

SSH dikembangkan pada 1995 sebagai pengganti Telnet dan rlogin. SSH **mengenkripsi seluruh data** antara client dan server, sehingga walaupun disadap isinya tidak terbaca. SSH berjalan pada **port 22** dan mendukung autentikasi username-password maupun **SSH key pair**.

**Kapan cocok untuk dipakai?**

- Mengelola router atau switch di jaringan nyata
- Saat password dan konfigurasi tidak boleh bisa disadap
- Sebagai standar remote access yang aman

##### Karakteristik

- **Port 22** - port default SSH
- **Terenkripsi** - seluruh komunikasi diamankan
- **Butuh hostname dan domain name** - syarat sebelum membuat RSA key
- **RSA key** - pasangan kunci enkripsi yang dibuat dengan `crypto key generate rsa`
- **`login local`** - autentikasi memakai username dan password lokal
- **Host key** - memverifikasi identitas server setiap koneksi

##### Kelebihan

- **Aman** - data terenkripsi dan integritasnya dicek dengan hashing
- **Server terverifikasi** - mencegah spoofing lewat host key
- **Fitur lengkap** - mendukung SCP, SFTP, port forwarding, dan kompresi

##### Kekurangan

- **Konfigurasi lebih kompleks** - butuh hostname, domain name, dan RSA key
- **Overhead sedikit lebih besar** - ada proses enkripsi dan key exchange

##### Konfigurasi

###### Interface Router sebagai gateway tiap subnet

```
Router>en
Router#conf t
Router(config)#interface fa0/0.1
Router(config-subif)#encapsulation dot1Q 1 native
Router(config-subif)#ip address 192.168.1.249 255.255.255.248
Router(config-subif)#exit
```

###### IP Switch (VLAN 1 Opsional)

```
Switch>enable
Switch#configure terminal
Switch(config)#interface vlan 1
Switch(config-if)#ip address 192.168.1.253 255.255.255.248
Switch(config-if)#no shutdown
```

###### SSH di Router

```
Router(config)#hostname R1
R1(config)#ip domain-name roni.org
R1(config)#username Rama secret 67890
R1(config)#crypto key generate rsa
How many bits in the modulus [512]: 1024
R1(config)#line vty 0 4
R1(config-line)#transport input ssh
R1(config-line)#login local
R1(config-line)#exit
R1(config)#enable secret 67890
R1(config)#exit
```

> **Perhatikan urutan:** hostname harus diubah dari default **sebelum** `crypto key generate rsa`, dan `ip domain-name` harus sudah ada. Kalau tidak, RSA key gagal dibuat.

**Uji:** Buka Command Prompt di salah satu PC, lalu SSH ke router.

```
C:\>ssh -l roni 192.168.10.1

Password:



roni>enable
Password:
roni#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
roni(config)#
```

| Perintah                        | Fungsi                                                                       |
| ------------------------------- | ---------------------------------------------------------------------------- |
| `enable` / `en`                 | Masuk ke privileged EXEC mode                                                |
| `configure terminal` / `conf t` | Masuk ke global configuration mode                                           |
| `interface gig0/0`              | Masuk ke konfigurasi interface router                                        |
| `ip address <ip> <subnet>`      | Memberi alamat IP pada interface                                             |
| `no shutdown`                   | Mengaktifkan interface                                                       |
| `username <nama> secret <pw>`   | Membuat akun lokal untuk autentikasi SSH                                     |
| `hostname <nama>`               | Mengganti nama perangkat, wajib diubah dari default sebelum generate RSA key |
| `ip domain-name <domain>`       | Menentukan domain name, dibutuhkan untuk generate RSA key                    |
| `crypto key generate rsa`       | Membuat pasangan kunci RSA untuk SSH                                         |
| `line vty 0 4`                  | Masuk ke konfigurasi line VTY 0 sampai 4                                     |
| `transport input ssh`           | Mengizinkan hanya SSH pada line VTY                                          |
| `login local`                   | Autentikasi memakai username dan password lokal                              |
| `enable secret <password>`      | Mengatur password terenkripsi untuk privileged EXEC mode                     |
| `exit`                          | Keluar dari mode konfigurasi saat ini                                        |
| `ssh -l <username> <ip>`        | Remote ke perangkat tujuan lewat SSH dengan username tertentu                |

### Telnet vs SSH

| Aspek                | Telnet                                           | SSH                                                                    |
| -------------------- | ------------------------------------------------ | ---------------------------------------------------------------------- |
| Port default         | 23                                               | 22                                                                     |
| Enkripsi             | Tidak ada (plain text)                           | Seluruh data terenkripsi                                               |
| Autentikasi          | Hanya username dan password, terkirim apa adanya | Password atau SSH key pair (private key tidak dikirim lewat jaringan)  |
| Integritas data      | Tidak ada pengecekan, rentan man-in-the-middle   | Dicek dengan hashing, perubahan data terdeteksi                        |
| Verifikasi server    | Tidak ada, rentan spoofing                       | Host key, ada peringatan jika berubah                                  |
| Performa             | Sedikit lebih ringan                             | Overhead enkripsi, tapi tidak signifikan pada penggunaan modern        |
| Fitur tambahan       | Hanya command-line jarak jauh                    | SCP, SFTP, port forwarding, kompresi                                   |
| Konfigurasi di Cisco | Sederhana (`line vty` dan `password`)            | Lebih kompleks (hostname, domain-name, RSA key, `transport input ssh`) |
| Rekomendasi          | Hanya untuk lab                                  | Standar untuk jaringan nyata                                           |

---

## Bagian 4: Access Control List (ACL)

Access Control List (ACL) adalah kumpulan aturan berurutan yang dikonfigurasi pada router atau switch Layer 3 untuk **mengizinkan (permit)** atau **menolak (deny)** paket yang melewati sebuah interface, berdasarkan IP sumber/tujuan, jenis protokol (TCP, UDP, ICMP), dan nomor port.

ACL bekerja seperti penjaga gerbang: setiap paket dicocokkan dengan daftar aturan, lalu diizinkan atau ditolak berdasarkan **aturan pertama yang cocok**.

### Skenario yang Dipakai

Melanjutkan topologi Inter-VLAN Routing: VLAN 10 (Finance) dan VLAN 20 (Sales) sudah terhubung lewat RoAS/SVI. Tambahkan **VLAN 30 (IT, 192.168.30.0/24, gateway 192.168.30.1)** pada router/switch yang sama. Kebijakan perusahaan:

| Pasangan VLAN     | Kebijakan                  |
| ----------------- | -------------------------- |
| VLAN 10 ↔ VLAN 20 | ✅ Boleh                    |
| VLAN 20 ↔ VLAN 30 | ✅ Boleh                    |
| VLAN 10 ↔ VLAN 30 | ❌ Diblokir, **kedua arah** |

###### Tambahan konfigurasi VLAN 30

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

> **Penting:** Pastikan ping antar ketiga VLAN sudah **berhasil semua** sebelum memasang ACL. Jika ACL dipasang saat routing belum beres, kamu tidak bisa membedakan penyebab gagalnya: routing atau ACL.

**Kapan cocok untuk dipakai?**

- Membatasi akses antar-departemen (misalnya Finance tidak boleh ke IT)
- Membatasi layanan tertentu (misalnya blokir HTTP ke server)
- Menyaring trafik yang masuk atau keluar dari sebuah segmen

##### Karakteristik

- **Berurutan (top-down)** - aturan diperiksa dari atas ke bawah, berhenti di aturan pertama yang cocok
- **Implicit deny** - di akhir setiap ACL ada `deny any` tersembunyi
- **Wildcard mask** - memakai wildcard, bukan subnet mask biasa
- **Per interface, per arah** - dipasang dengan `ip access-group` pada arah `in` atau `out`
- **Hanya menyaring trafik yang lewat router** - trafik yang dibuat router sendiri tidak terkena ACL
- **Maksimal satu ACL per arah, per protokol** pada setiap interface

##### Kelebihan

- **Keamanan** - membatasi akses ke perangkat atau subnet tertentu
- **Kontrol trafik** - membatasi jenis trafik untuk menghemat bandwidth
- **Fleksibel** - bisa disaring berdasarkan IP, protokol, dan port
- **Serbaguna** - juga dipakai untuk filtering rute, QoS, NAT, dan VPN

##### Kekurangan

- **Mudah salah urutan** - aturan yang salah urut bisa memblokir trafik yang seharusnya boleh
- **Implicit deny menjebak** - lupa `permit` di akhir membuat semua trafik terblokir
- **Sulit dikelola** jika besar dan hanya memakai nomor
- **Standard ACL terbatas** - hanya mengenali IP sumber

### Cara Kerja ACL

Setiap ACL terdiri dari satu atau lebih *access control entries* (ACE) yang diproses top-down:

1. Paket dibandingkan dengan ACE pertama.
2. Jika cocok, aksi *permit* atau *deny* langsung dijalankan dan pencocokan berhenti.
3. Jika tidak cocok, paket dibandingkan dengan ACE berikutnya, dan seterusnya.
4. Jika tidak cocok dengan ACE mana pun, paket ditolak oleh **implicit deny**.

> **Implicit Deny:** ACL yang tujuannya "blokir sebagian saja" **wajib diakhiri** dengan `permit any` (Standard) atau `permit ip any any` (Extended).

**Contoh alur (ACL 110 pada bagian Extended Numbered):** paket dari PC1 (192.168.10.10) ke PC3 (192.168.30.10):

```
Baris 10: deny ip 192.168.10.0/24 -> 192.168.30.0/24   ← COCOK, paket DITOLAK. Selesai.
```

Paket dari PC1 ke PC2 (192.168.20.10):

```
Baris 10: deny ip 192.168.10.0/24 -> 192.168.30.0/24   ← tujuan bukan VLAN 30, tidak cocok
Baris 20: deny ip 192.168.30.0/24 -> 192.168.10.0/24   ← sumber bukan VLAN 30, tidak cocok
Baris 30: permit ip any any                            ← COCOK, paket DIIZINKAN.
```

###### Wildcard Mask

Wildcard mask berkebalikan dengan subnet mask: bit `0` berarti oktet harus cocok persis, bit `1` berarti oktet boleh bernilai apa saja.

| Kebutuhan                   | Subnet Mask     | Wildcard Mask   |
| --------------------------- | --------------- | --------------- |
| Host tunggal (192.168.10.5) | 255.255.255.255 | 0.0.0.0         |
| Subnet /24 (192.168.10.0)   | 255.255.255.0   | 0.0.0.255       |
| Subnet /16 (172.16.0.0)     | 255.255.0.0     | 0.0.255.255     |
| Semua alamat (any)          | 0.0.0.0         | 255.255.255.255 |

Cara cepat: kurangi setiap oktet subnet mask dari 255. Contoh `255.255.255.192` menjadi wildcard `0.0.0.63`. Kata kunci `any` = `0.0.0.0 255.255.255.255`, dan `host <ip>` = `<ip> 0.0.0.0`. Jadi `192.168.10.0 0.0.0.255` dibaca: "tiga oktet pertama harus 192.168.10, oktet terakhir bebas", yaitu seluruh VLAN 10.

### Jenis-Jenis ACL

ACL dibedakan dengan **dua cara** yang saling bersilangan:

1. **Berdasarkan kriteria filter:** *Standard* (hanya IP sumber) dan *Extended* (IP sumber, IP tujuan, protokol, port).
2. **Berdasarkan cara identifikasi:** *Numbered* (nomor) dan *Named* (nama).

|              | Standard                         | Extended                         |
| ------------ | -------------------------------- | -------------------------------- |
| **Numbered** | Nomor 1–99 / 1300–1999           | Nomor 100–199 / 2000–2699        |
| **Named**    | `ip access-list standard <nama>` | `ip access-list extended <nama>` |

> **Ringkasnya:** *Standard vs Extended* menjawab "**apa** yang bisa disaring". *Numbered vs Named* menjawab "**bagaimana** ACL diberi identitas".

#### Numbered ACL

ACL yang diidentifikasi dengan **nomor**. Rentang nomor menentukan jenisnya.

- Dibuat dari global configuration dengan `access-list <nomor> ...`
- Setiap baris dimasukkan satu per satu dengan nomor yang sama
- Dengan cara `access-list` biasa, satu baris tidak bisa dihapus sendiri. `no access-list <nomor>` menghapus **seluruh** ACL
- Kurang deskriptif karena hanya angka

> **Jangan tertukar:** nomor ACL **tidak ada hubungannya** dengan VLAN ID. Pada contoh di bawah dipakai nomor 11 dan 12 agar tidak tertukar dengan VLAN 10, 20, dan 30.

###### Standard Numbered ACL

Menyaring hanya berdasarkan **IP sumber**, dipasang **dekat tujuan**.

```
access-list <1-99> {permit | deny} <ip-sumber> <wildcard>
```

Karena tidak mengenali IP tujuan, satu ACL hanya menutup **satu arah**. Untuk memblokir VLAN 10 ↔ VLAN 30 dibutuhkan **dua ACL**.

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

- ACL 11 di `Fa0/0.30` arah **out**: paket yang keluar ke VLAN 30 diperiksa, jika sumbernya VLAN 10 dibuang. VLAN 20 lolos lewat `permit any`.
- ACL 12 di `Fa0/0.10` arah **out** menutup arah sebaliknya.
- `permit any` wajib ada; tanpanya implicit deny memblokir **semua** trafik, termasuk VLAN 20.
- **Keterbatasan:** memblokir *seluruh* trafik dari sumber itu dan tidak bisa memilih protokol atau port.

###### Extended Numbered ACL

Menyaring berdasarkan **IP sumber, IP tujuan, protokol, dan port**, dipasang **dekat sumber**.

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

- **`deny` pertama** menolak VLAN 10 → VLAN 30.
- **`deny` kedua** menolak VLAN 30 → VLAN 10. Wajib ada karena ACL memproses satu arah per paket.
- **`permit ip any any`** wajib ada agar trafik lain (VLAN 10 ↔ 20 dan VLAN 20 ↔ 30) tetap boleh.
- ACL dipasang di **dua sub-interface** (VLAN 10 dan VLAN 30, inbound) karena ACL hanya memeriksa paket yang lewat di interface tempat ia dipasang.
- Tidak perlu dipasang di VLAN 20 karena tidak ada kriteria `deny` yang menyebut VLAN 20.

**Contoh tambahan, filter berdasarkan protokol/port:** VLAN 10 dilarang mengakses web server (HTTP, port 80) di VLAN 30, trafik lain tetap boleh.

```
R1(config)# access-list 120 deny tcp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 eq 80
R1(config)# access-list 120 permit ip any any
R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# ip access-group 120 in
```

Contoh ini **tidak bisa** dibuat dengan Standard ACL karena butuh IP tujuan dan nomor port. Ping VLAN 10 ke VLAN 30 tetap berhasil, hanya akses HTTP yang ditolak.

#### Named ACL

ACL yang diidentifikasi dengan **nama** deskriptif.

- Dibuat dengan `ip access-list {standard | extended} <nama>`, lalu masuk ke mode `config-std-nacl` atau `config-ext-nacl`
- Nama deskriptif (misal `BLOCK_V10_TO_V30`) sehingga mudah dibaca
- Bisa menyisipkan dan menghapus satu baris dengan nomor *sequence*
- Di dalam mode ACL, perintah **tidak diawali `access-list`**, langsung `permit` atau `deny`
- Nama *case-sensitive* dan tidak boleh mengandung spasi

###### Standard Named ACL

```
blokir trafik DARI VLAN 40 yang menuju VLAN 50 =====
R1> enable
R1# configure terminal
R1(config)# ip access-list standard BLOCK_40_50
R1(config-std-nacl)# deny 192.168.40.0 0.0.0.15 (NA dan WILCARD)
R1(config-std-nacl)# permit any
R1(config-std-nacl)# exit

R1(config)# interface FastEthernet 0/0.50
R1(config-subif)# ip access-group BLOCK_40_50 out
R1(config-subif)# exit

```

Sama seperti Standard Numbered, dibutuhkan dua ACL. Bedanya hanya pada penulisan: nama menggantikan nomor.

###### Extended Named ACL

```
R-Tengah(config)#ip access-list extended 40_KE_50
R-Tengah(config-ext-nacl)#permit icmp 192.168.40.0 0.0.0.15 192.168.50.0 0.0.0.15 echo-reply
R-Tengah(config-ext-nacl)#permit tcp 192.168.40.0 0.0.0.15 192.168.50.0 0.0.0.15 established
R-Tengah(config-ext-nacl)#deny ip 192.168.40.0 0.0.0.15 192.168.50.0 0.0.0.15
R-Tengah(config-ext-nacl)#permit ip any any
R-Tengah(config-ext-nacl)#exit


R-Tengah(config)#interface fa0/1.40
R-Tengah(config-subif)#ip access-group 40_KE_50 ins-group BLOCK_FINANCE_IT in
R1(config-subif)# exit
```

**Menyisipkan baris tertentu** (keunggulan Named ACL). Setiap baris diberi nomor *sequence* (10, 20, 30, ...), jadi baris baru bisa disisipkan di antaranya. Contoh: VLAN 10 dan VLAN 30 tetap boleh saling mengirim email (SMTP, port 25).

```
R1(config)# ip access-list extended BLOCK_FINANCE_IT

! Sequence 5 dan 6 lebih kecil dari 10, jadi diperiksa SEBELUM aturan deny
! Seq 5: VLAN 10 (klien) -> server SMTP di VLAN 30, port tujuan 25
R1(config-ext-nacl)# 5 permit tcp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 eq 25

! Seq 6: balasan dari VLAN 30 (port sumber 25) kembali ke VLAN 10
R1(config-ext-nacl)# 6 permit tcp 192.168.30.0 0.0.0.255 eq 25 192.168.10.0 0.0.0.255
R1(config-ext-nacl)# exit
```

> **Kenapa butuh dua baris?** Koneksi TCP itu dua arah. Klien mengirim ke port 25 (baris 5), lalu server membalas **dari port 25** ke klien (baris 6). Jika hanya baris 5 yang ada, balasan terkena `deny` kedua dan koneksi email tidak pernah selesai.

Hasil `show access-lists BLOCK_FINANCE_IT` setelah penyisipan:

```
Extended IP access list BLOCK_FINANCE_IT
    5 permit tcp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 eq smtp
    6 permit tcp 192.168.30.0 0.0.0.255 eq smtp 192.168.10.0 0.0.0.255
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
    20 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
    30 permit ip any any
```

Menghapus satu baris saja (misal sequence 6):

```
R1(config)# ip access-list extended BLOCK_FINANCE_IT
R1(config-ext-nacl)# no 6
R1(config-ext-nacl)# exit
```

> **Catatan:** Pada IOS modern, Numbered ACL juga bisa diedit per baris lewat `ip access-list extended 110`. Namun cara lama `access-list 110 ...` tetap tidak bisa menghapus satu baris saja.

### Perbandingan Jenis ACL

| Aspek                         | Standard Numbered                 | Extended Numbered                 | Standard Named                    | Extended Named                    |
| ----------------------------- | --------------------------------- | --------------------------------- | --------------------------------- | --------------------------------- |
| Kriteria filter               | IP sumber                         | IP sumber, tujuan, protokol, port | IP sumber                         | IP sumber, tujuan, protokol, port |
| Identifikasi                  | Nomor 1–99 / 1300–1999            | Nomor 100–199 / 2000–2699         | Nama                              | Nama                              |
| Perintah pembuatan            | `access-list 11 ...`              | `access-list 110 ...`             | `ip access-list standard <nama>`  | `ip access-list extended <nama>`  |
| Penempatan ideal              | Dekat tujuan                      | Dekat sumber                      | Dekat tujuan                      | Dekat sumber                      |
| Edit per baris                | Terbatas (cara lama tidak bisa)   | Terbatas (cara lama tidak bisa)   | Ya (sequence number)              | Ya (sequence number)              |
| Blokir pasangan VLAN dua arah | ❌ Butuh 2 ACL, tidak kenal tujuan | ✅ 1 ACL, dipasang di 2 interface  | ❌ Butuh 2 ACL, tidak kenal tujuan | ✅ 1 ACL, mudah dikelola           |

### Aturan Penempatan ACL

**Arah inbound dan outbound** selalu dilihat dari sudut pandang **router/interface**:

- **Inbound (`in`)**: paket diperiksa saat **masuk** ke interface, sebelum diproses tabel routing. Lebih efisien jika paket memang akan ditolak.
- **Outbound (`out`)**: paket diproses routing dulu, baru diperiksa saat akan **keluar** dari interface.

**Aturan lokasi:**

- **Standard ACL** → dekat **tujuan**, karena tidak mengenali IP tujuan. Jika dekat sumber, ia memblokir sumber itu ke *semua* tujuan.
- **Extended ACL** → dekat **sumber**, agar paket yang akan ditolak dibuang lebih awal.
- **Pembatasan dua arah antar dua VLAN** (VLAN 10 ↔ VLAN 30) butuh ACL yang sama di **kedua sub-interface/SVI**, arah inbound, karena tiap sisi hanya melihat trafik yang masuk dari VLAN-nya sendiri.

**Menerapkan ACL pada metode SVI:** jika memakai switch Layer 3, ACL dipasang pada interface SVI, bukan sub-interface. Isi ACL-nya sama.

```
SW1(config)# interface vlan 10
SW1(config-if)# ip access-group 110 in
SW1(config-if)# exit
SW1(config)# interface vlan 30
SW1(config-if)# ip access-group 110 in
SW1(config-if)# exit
```

### Verifikasi ACL

| Perintah                         | Fungsi                                                                     |
| -------------------------------- | -------------------------------------------------------------------------- |
| `show access-lists`              | Menampilkan semua ACL beserta jumlah paket yang cocok (*match*) tiap baris |
| `show access-lists <nomor/nama>` | Detail satu ACL tertentu                                                   |
| `show ip interface <interface>`  | ACL yang diterapkan pada interface beserta arahnya                         |
| `show running-config`            | Konfigurasi ACL lengkap sesuai urutan baris                                |
| `ping` / `traceroute`            | Menguji apakah trafik diizinkan atau ditolak                               |

**Uji:** Lakukan ping dari **Command Prompt masing-masing PC**.

```
PC1> ping 192.168.20.10    ! HARUS berhasil (VLAN 10 -> VLAN 20)
PC2> ping 192.168.30.10    ! HARUS berhasil (VLAN 20 -> VLAN 30)
PC1> ping 192.168.30.10    ! HARUS gagal (VLAN 10 -> VLAN 30)
PC3> ping 192.168.10.10    ! HARUS gagal (VLAN 30 -> VLAN 10)
```

> **Kenapa ping dari PC, bukan dari router?** Perintah `ping <tujuan> source <ip>` di router hanya bisa memakai IP milik **router itu sendiri** sebagai sumber, bukan IP milik PC. Untuk menguji ACL seperti kondisi sebenarnya, ping langsung dari PC.

Hasil gagal biasanya tampil sebagai `Request timed out` atau `Destination host unreachable`. Setelah ping, cek jumlah paket yang cocok:

```
R1# show access-lists 110
Extended IP access list 110
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 (4 match(es))
    20 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255 (4 match(es))
    30 permit ip any any (16 match(es))
```

Angka *match(es)* naik setiap ada paket yang cocok, sehingga bisa dipakai untuk memastikan baris mana yang bekerja.

**Masalah umum:**

| Gejala                                                                 | Penyebab                                                   | Solusi                                                                 |
| ---------------------------------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| Semua trafik terblokir                                                 | Lupa `permit any` di akhir ACL                             | Tambahkan baris permit eksplisit sebelum implicit deny                 |
| ACL tidak berefek                                                      | Belum ada `ip access-group` di interface                   | Terapkan ACL pada interface dengan arah yang benar                     |
| ACL tidak berefek (sudah dipasang)                                     | Salah arah (`in` tertukar `out`) atau salah interface      | Cek dengan `show ip interface <interface>`                             |
| VLAN 30 → VLAN 10 masih lolos padahal VLAN 10 → VLAN 30 sudah diblokir | Hanya satu baris `deny` (satu arah)                        | Tambahkan baris `deny` untuk arah sebaliknya                           |
| Trafik yang seharusnya diizinkan ditolak                               | Urutan ACE salah                                           | Susun ulang dari yang paling spesifik ke paling umum                   |
| Trafik sumber lain ikut terblokir                                      | Standard ACL terlalu dekat sumber                          | Pindahkan ke interface dekat tujuan, atau ganti ke Extended ACL        |
| Error `% Invalid input` saat membuat ACL                               | Nomor tidak sesuai jenis (misal `permit ip` pada nomor 10) | Standard (1–99) hanya menerima IP sumber; pakai 100–199 untuk Extended |
| Email/TCP tidak jalan padahal sudah di-permit                          | Hanya satu arah yang di-permit                             | Tambahkan permit untuk balasan (port sumber = port layanan)            |

---

### Urutan Konfigurasi

| No  | Perangkat           | Yang dikonfigurasi                                                                            |
| --- | ------------------- | --------------------------------------------------------------------------------------------- |
| 1   | Access Point        | SSID dan keamanan WPA2-PSK                                                                    |
| 2   | Smartphone / Laptop | Hubungkan ke SSID (laptop: ganti modul WPC300N dulu)                                          |
| 3   | Router / Switch     | `line console 0` + `login`, lalu `enable secret`, lalu `hostname`                             |
| 4   | Switch              | IP VLAN 1, lalu Telnet (`line vty`, `transport input telnet`)                                 |
| 5   | Router              | IP interface, lalu SSH (username, domain-name, RSA key, `transport input ssh`, `login local`) |
| 6   | Switch dan Router   | Tambah VLAN 30 dan sub-interface VLAN 30                                                      |
| 7   | PC                  | **Pastikan ping antar VLAN berhasil sebelum ACL**                                             |
| 8   | Router              | Buat ACL, lalu `ip access-group` pada interface                                               |
| 9   | PC                  | Uji ping, `show access-lists`, dan cek *match(es)*                                            |

### Latihan Praktikum

1. Buat topologi sederhana di Cisco Packet Tracer dengan **1 Router** dan **1 PC** (hubungkan langsung dengan kabel console).
2. Konfigurasikan **password pada line console 0** (contoh: `12345`) dan aktifkan `login`.
3. Screenshot hasil uji verifikasi (saat membuka kembali akses ke Router, harus muncul prompt `Password:`).

### Istilah

###### WLAN (Wireless Local Area Network)

Jaringan lokal yang menghubungkan perangkat tanpa kabel fisik, memakai gelombang radio

###### IEEE 802.11

Standar teknis WLAN, secara komersial dikenal sebagai Wi-Fi

###### Access Point (AP)

Perangkat pusat WLAN yang menjembatani perangkat nirkabel dengan jaringan kabel

###### SSID

Nama jaringan wireless yang tampil saat client mencari Wi-Fi

###### WPA2-PSK

Jenis keamanan WLAN dengan enkripsi dan satu password bersama (Pre-Shared Key)

###### WPC300N

Modul wireless yang dipasang pada Laptop di Packet Tracer agar bisa terhubung ke WLAN

###### CLI (Command Line Interface)

Antarmuka berbasis perintah untuk mengonfigurasi perangkat Cisco

###### Line Console 0

Line untuk akses lewat port Console fisik pada router atau switch

###### Privilege Mode

Mode dengan hak akses tinggi (prompt `#`), diamankan dengan `enable secret`

###### enable secret

Perintah untuk memberi password terenkripsi pada Privilege Mode

###### Hostname

Nama perangkat Cisco yang tampil pada prompt CLI

###### VTY (Virtual Terminal)

Line virtual untuk akses remote lewat jaringan (Telnet atau SSH)

###### Telnet

Protokol remote access lama pada port 23, data dikirim tanpa enkripsi

###### SSH (Secure Shell)

Protokol remote access aman pada port 22, data dienkripsi

###### RSA Key

Pasangan kunci enkripsi yang dibuat router untuk SSH

###### Sniffing

Penyadapan data yang lewat di jaringan, misalnya dengan Wireshark

###### ACL (Access Control List)

Kumpulan aturan berurutan untuk mengizinkan atau menolak paket pada sebuah interface

###### ACE (Access Control Entry)

Satu baris aturan di dalam ACL

###### Implicit Deny

Aturan `deny any` tersembunyi di akhir setiap ACL

###### Wildcard Mask

Penentu rentang IP pada ACL; bit `0` harus cocok, bit `1` bebas

###### Standard ACL

ACL yang hanya menyaring berdasarkan IP sumber, dipasang dekat tujuan

###### Extended ACL

ACL yang menyaring berdasarkan IP sumber, tujuan, protokol, dan port, dipasang dekat sumber

###### Numbered ACL

ACL yang diidentifikasi dengan nomor (1–99 / 100–199)

###### Named ACL

ACL yang diidentifikasi dengan nama dan bisa diedit per baris lewat sequence number

###### Sequence Number

Nomor urut baris dalam ACL yang dipakai untuk menyisipkan atau menghapus satu baris

###### ip access-group

Perintah untuk memasang ACL pada interface dengan arah `in` atau `out`

###### Inbound / Outbound

Arah pemeriksaan ACL: saat paket masuk ke interface (`in`) atau keluar dari interface (`out`)

###### SVI (Switch Virtual Interface)

Interface virtual VLAN pada switch Layer 3, tempat ACL bisa dipasang

###### Router-on-a-Stick (RoAS)

Teknik inter-VLAN routing memakai satu kabel trunk ke router, dengan satu sub-interface per VLAN
