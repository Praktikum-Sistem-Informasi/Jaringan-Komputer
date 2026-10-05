# DHCP, DNS, dan Web Server

### Topologi yang Digunakan

Topologi pada praktikum ini memakai **Router0** dan **Router1**, dua switch, beberapa PC, dan satu **Server0**. Ada dua segmen LAN yang mendapat IP otomatis, dan keduanya dilayani dengan cara yang berbeda:

- **VLAN 40 (Administrasi)**, `192.168.40.0/28`: IP dibagikan oleh **Router0**. Segmen kecil dan konfigurasinya paling sederhana.
- **VLAN 50 (HRD)**, `192.168.50.0/28`, gateway `192.168.50.14`: IP dibagikan oleh **Server0**. Server berada di segmen lain (`192.168.100.0/29`), jadi request dari PC harus diteruskan lewat **relay**.
- **Server0** (`192.168.100.2`, gateway `192.168.100.1`) menjalankan tiga layanan sekaligus: **DHCP, DNS, dan HTTP**.

Alur besarnya: PC dapat IP dari DHCP, lalu mengetik nama domain di browser, nama itu diterjemahkan oleh DNS menjadi IP, dan halaman web diambil dari Web Server.

### Dynamic Host Configuration Protocol (DHCP)

DHCP adalah layanan yang memberikan **IP address, subnet mask, default gateway, dan DNS server** ke client secara otomatis, jadi tidak perlu diisi satu per satu secara manual. Proses pemberiannya dikenal sebagai **DORA**:

1. **Discover**: PC berteriak (broadcast) mencari DHCP server.
2. **Offer**: Server menawarkan satu IP yang masih kosong.
3. **Request**: PC meminta IP yang ditawarkan tersebut.
4. **Acknowledge**: Server menyetujui, dan PC resmi memakai IP itu selama masa **lease**.

**Kapan cocok untuk dipakai?**

- Jaringan dengan banyak PC yang malas kalau harus diatur IP-nya satu per satu
- Jaringan yang IP client-nya sering berganti (laptop, HP, tamu)
- Jaringan yang ingin pengaturan IP terpusat di satu tempat

##### Karakteristik

- **Otomatis** - client tidak perlu diatur manual, cukup pilih mode DHCP
- **Proses DORA** - Discover, Offer, Request, Acknowledge
- **Memakai broadcast** - Discover dikirim ke semua perangkat di segmen yang sama, **tidak bisa melewati router** secara default
- **Pool** - daftar rentang IP yang boleh dibagikan ke client
- **Lease time** - lama waktu client boleh memakai IP sebelum harus memperpanjang
- **Excluded address** - IP yang dikecualikan dari pool (misalnya IP gateway), agar tidak dibagikan ke client
- **Bisa dijalankan di Router atau Server** - Router cocok untuk segmen kecil, Server cocok untuk segmen besar
- **Butuh relay (`ip helper-address`)** jika DHCP server berada di segmen yang berbeda dari client

##### Kelebihan

- **Praktis** - Client langsung dapat IP, gateway, dan DNS tanpa setting manual
- **Mengurangi salah konfigurasi** - Tidak ada risiko dua PC memakai IP yang sama karena salah ketik
- **Terpusat** - Perubahan (misalnya ganti DNS) cukup dilakukan di satu tempat
- **Hemat IP** - IP yang tidak dipakai bisa dipakai client lain setelah lease habis

##### Kekurangan

- **Tergantung DHCP server** - Kalau server mati atau tidak terjangkau, PC mendapat IP APIPA (`169.254.x.x`) dan tidak bisa berkomunikasi
- **Butuh relay untuk lintas segmen** - Tanpa `ip helper-address`, request dari PC tidak sampai ke server
- **Scope tidak boleh bentrok** - Jika dua DHCP membagikan network yang sama, bisa terjadi IP ganda
- **DHCP di router terbatas** - Kurang cocok untuk jaringan besar karena membebani router

##### Konfigurasi

```
Router(config)# ip dhcp pool [Nama Pool]
Router(dhcp-config)# net [NA] [Subnet Mask]
Router(dhcp-config)# default-router [Gateway]
Router(dhcp-config)# dns-server [IP DNS]
Router(dhcp-config)# exit
```

###### DHCP di Router Kiri (VLAN 10 dan 20)

```
! Buat DHCP pool marketing
Router0(config)# ip dhcp pool marketing
Router0(dhcp-config)# network 192.168.10.0 255.255.255.240
Router0(dhcp-config)# default-router 192.168.10.14
Router0(dhcp-config)# dns-server 192.168.100.2
Router0(dhcp-config)# exit

! Buat DHCP pool sales
Router0(config)# ip dhcp pool sales
Router0(dhcp-config)# network 192.168.20.0 255.255.255.240
Router0(dhcp-config)# default-router 192.168.20.14
Router0(dhcp-config)# dns-server 192.168.100.2
Router0(dhcp-config)# exitx
```

###### DHCP di Router Tengah (VLAN 30 dan 40)

```
! Buat DHCP pool keamanan
Router0(config)# ip dhcp pool keamanan
Router0(dhcp-config)# network 192.168.30.0 255.255.255.240
Router0(dhcp-config)# default-router 192.168.30.14
Router0(dhcp-config)# dns-server 192.168.100.2
Router0(dhcp-config)# exit

! Buat DHCP pool administrasi
Router0(config)# ip dhcp pool administrasi
Router0(dhcp-config)# network 192.168.40.0 255.255.255.240
Router0(dhcp-config)# default-router 192.168.40.14
Router0(dhcp-config)# dns-server 192.168.100.2
Router0(dhcp-config)# exit
```

> `excluded-address` menahan IP `.1` dan `.2` supaya tidak dibagikan ke PC. IP `.1` dipakai gateway. Karena itu, PC di VLAN 40 akan mendapat IP mulai dari `192.168.40.3` sampai `192.168.40.14`.

**Uji:** Set PC di VLAN 40 ke **DHCP**, lalu jalankan `ipconfig` dan cek IP-nya sesuai pool. Di Router0, jalankan `show ip dhcp binding` untuk melihat IP client yang tercatat.

###### Inter-VLAN Routing di Router1 (persiapan VLAN 50)

Koneksi Router1 ke Switch2 adalah **trunk** (membawa banyak VLAN), sehingga IP gateway VLAN 50 dipasang di **subinterface**, bukan di interface fisik. Teknik ini disebut **router-on-a-stick**.

```
Router1(config)# interface FastEthernet0/0
Router1(config-if)# no ip address
Router1(config-if)# no shutdown
Router1(config-if)# exit

Router1(config)# interface FastEthernet0/0.50
Router1(config-subif)# encapsulation dot1Q 50
Router1(config-subif)# ip address 192.168.50.14 255.255.255.240
Router1(config-subif)# ip helper-address 192.168.100.2
Router1(config-subif)# exit

! Interface ke Server0
Router1(config)# interface FastEthernet1/1
Router1(config-if)# ip address 192.168.100.1 255.255.255.248
Router1(config-if)# no shutdown
```

###### Switch2

```
! Port ke Router1 wajib trunk
Switch2(config)# interface FastEthernet0/1
Switch2(config-if)# switchport mode trunk
```

Cek dengan `show interfaces trunk`. Kalau port itu tidak trunk, subinterface `.50` tidak akan menerima frame apa pun dari VLAN 50.

> **Tentang `ip helper-address`:** Broadcast Discover dari PC tidak bisa menyeberang router. Perintah ini membuat Router1 mengubah broadcast tersebut menjadi unicast dan mengirimkannya langsung ke Server0 (`192.168.100.2`). Balasan Offer dan Acknowledge dikembalikan ke PC lewat Router1 juga. Kalau perintah ini tidak ada atau alamatnya salah, PC akan tetap mendapat APIPA (`169.254.x.x`) walaupun DHCP di server sudah benar.

###### DHCP di Server0 (VLAN 50)

**1. Beri Server0 IP statis** (Desktop > IP Configuration > Static):

| Kolom           | Isi               |
| --------------- | ----------------- |
| IP Address      | `192.168.100.2`   |
| Subnet Mask     | `255.255.255.248` |
| Default Gateway | `192.168.100.1`   |

**2. Aktifkan DHCP** (Config/Services > DHCP):

| Kolom                   | Isi                                                      |
| ----------------------- | -------------------------------------------------------- |
| Service                 | **On**                                                   |
| Default Gateway         | `192.168.50.14`                                          |
| DNS Server              | `192.168.100.2` (IP server sendiri, **bukan** IP router) |
| Start IP Address        | `192.168.50.1`                                           |
| Subnet Mask             | `255.255.255.240`                                        |
| Maximum Number of Users | `13`                                                     |

Klik **Add** (jika pool baru), lalu **Save**.

> Kenapa maksimal 13? Network `192.168.50.0/28` punya host valid `.1` sampai `.14`. Karena `.14` dipakai gateway, tersisa `.1` sampai `.13` atau 13 alamat.

**Uji:** Set PC di VLAN 50 ke **DHCP**, lalu jalankan `ipconfig`. IP harus berada di rentang `.1` sampai `.13`, bukan `169.254.x.x`. Untuk melihat daftar client, buka panel DHCP di Server0. Perintah `show ip dhcp binding` di router **tidak** menampilkan client VLAN 50, karena datanya tercatat di server.

### Router vs Server sebagai DHCP

Dalam topologi ini keduanya dipakai bersamaan. Syaratnya, **network yang dibagikan harus berbeda** supaya tidak bentrok.

| Aspek             | DHCP di Router0 (VLAN 40)               | DHCP di Server0 (VLAN 50)          |
| ----------------- | --------------------------------------- | ---------------------------------- |
| Network           | 192.168.40.0/28                         | 192.168.50.0/28                    |
| IP yang dibagikan | 192.168.40.3 - 192.168.40.14            | 192.168.50.1 - 192.168.50.13       |
| Cara konfigurasi  | CLI (`ip dhcp pool`)                    | GUI (Config/Services > DHCP)       |
| Perlu relay?      | Tidak, karena satu segmen dengan router | Ya, `ip helper-address` di Router1 |
| Cek client        | `show ip dhcp binding` di router        | Panel DHCP di Server0              |
| Cocok untuk       | Segmen kecil / lokal                    | Segmen besar, manajemen terpusat   |

**Kenapa perlu DHCP Server, bukan hanya router?**

- Sanggup melayani lebih banyak permintaan IP dengan lebih stabil
- Pengaturan dan pencatatan terpusat di satu titik
- Mudah dikembangkan tanpa membebani router
- Satu server bisa sekaligus melayani DNS dan HTTP, seperti pada topologi ini

### Domain Name System (DNS)

DNS berfungsi seperti **buku telepon internet**: menerjemahkan **nama** (misalnya `web-praktisi.local`) menjadi **alamat IP** (`192.168.100.2`). Dengan DNS, kita cukup mengingat nama, bukan deretan angka.

**Kapan cocok untuk dipakai?**

- Saat ada server yang ingin diakses dengan nama, bukan IP
- Saat IP server bisa berubah, tetapi namanya ingin tetap sama

##### Karakteristik

- **A Record** - jenis record yang memetakan nama ke alamat IPv4
- **Resolusi nama** - client bertanya ke DNS server, lalu DNS server menjawab dengan IP
- **Alamat DNS diberikan lewat DHCP** - client tidak perlu mengisi DNS secara manual
- **Harus di-Add lalu Save** - record baru tersimpan jika sudah masuk ke tabel

##### Konfigurasi

###### DNS di Server0

1. Buka **Config/Services > DNS**, set **DNS Service: On**
2. Isi Resource Record:

| Kolom   | Isi                  |
| ------- | -------------------- |
| Type    | `A Record`           |
| Name    | `web-praktisi.local` |
| Address | `192.168.100.2`      |

3. Klik **Add** dulu (memasukkan ke tabel), baru **Save**
4. Pastikan record muncul di tabel. Kalau tabel masih kosong, record belum tersimpan dan `nslookup` akan gagal.

###### Di sisi Client

Kolom DNS Server di PC akan terisi otomatis `192.168.100.2` jika IP didapat lewat DHCP yang sudah diatur dengan benar (`dns-server` di pool router untuk VLAN 40, atau kolom DNS Server di pool server untuk VLAN 50).

**Uji:** Di Command Prompt PC, jalankan `nslookup web-praktisi.local`. Hasilnya harus resolve ke `192.168.100.2`.

### Web Server (HTTP)

Web Server adalah komputer yang menyimpan dan menyajikan halaman web lewat protokol **HTTP**. Saat kita mengetik alamat di browser, browser meminta halaman ke server, lalu server mengirimkan file `index.html`.

##### Konfigurasi

###### HTTP di Server0

1. Buka **Config/Services > HTTP**, set **HTTP: On**
2. Edit `index.html`, contoh sederhana:

```html
<html>
<head>
<title>Web Server PRAKTISI</title>
</head>
<body>

<h1>Halo, ini Web Server VLAN 50</h1>
<p>Dikonfigurasi oleh: [nama kamu]</p>
<p>Pertemuan 6 - DHCP, DNS, Web Server</p>

</body>
</html>
```

3. Klik **Save**

**Uji (urutan disarankan):**

1. Di Web Browser PC, ketik `http://192.168.100.2`. Ini memastikan HTTP jalan tanpa bergantung pada DNS.
2. Jika berhasil, lanjut ketik `http://web-praktisi.local`. Ini membuktikan DNS dan HTTP sudah nyambung.

> **Kalau langkah 1 gagal:** cek PC sudah dapat IP dari DHCP (bukan APIPA), lalu coba `ping 192.168.100.2`. Kalau ping gagal, masalahnya di routing atau `ip helper-address`, bukan di HTTP. **Kalau langkah 1 berhasil tapi langkah 2 gagal:** cek record DNS sudah di-**Add** dan di-**Save**, cek kolom DNS Server di pool DHCP sudah `192.168.100.2`, lalu renew DHCP di PC.

### Urutan Konfigurasi

| No  | Perangkat | Yang dikonfigurasi                                       |
| --- | --------- | -------------------------------------------------------- |
| 1   | Router0   | IP interface + DHCP pool VLAN 40                         |
| 2   | Switch2   | Port ke Router1 dijadikan **trunk**                      |
| 3   | Router1   | Subinterface `Fa0/0.50` (dot1Q 50) + `ip helper-address` |
| 4   | Router1   | Interface `Fa1/1` ke Server0 (`192.168.100.1/29`)        |
| 5   | Server0   | IP statis, lalu aktifkan DHCP, DNS, dan HTTP             |
| 6   | PC        | Uji: DHCP binding, `nslookup`, lalu Web Browser          |

### Istilah

###### DHCP (Dynamic Host Configuration Protocol)

Layanan yang memberi IP address, subnet mask, gateway, dan DNS ke client secara otomatis

###### DORA

Empat tahap proses DHCP: Discover, Offer, Request, Acknowledge

###### Pool

Rentang alamat IP yang boleh dibagikan oleh DHCP server ke client

###### Lease Time

Lama waktu client boleh memakai IP dari DHCP sebelum harus memperpanjang

###### Excluded Address

IP yang dikecualikan dari pool agar tidak dibagikan ke client (misalnya IP gateway)

###### APIPA

Alamat `169.254.x.x` yang dipakai PC secara otomatis ketika gagal mendapat IP dari DHCP; tandanya request tidak sampai ke server

###### Trunk

Port yang membawa lalu lintas banyak VLAN sekaligus, biasanya antara switch dan router

###### Router-on-a-Stick

Teknik inter-VLAN routing memakai satu kabel trunk ke router, dengan satu subinterface untuk tiap VLAN

###### Subinterface

Interface virtual di dalam satu interface fisik router, dipakai untuk melayani satu VLAN (contoh: `Fa0/0.50`)

###### Encapsulation dot1Q

Perintah yang memberi tahu subinterface VLAN nomor berapa yang dilayaninya (contoh: `encapsulation dot1Q 50`)

###### ip helper-address

Perintah yang membuat router meneruskan broadcast DHCP sebagai unicast ke DHCP server di segmen lain (relay)

###### DNS (Domain Name System)

Layanan yang menerjemahkan nama domain menjadi alamat IP

###### A Record

Data di DNS server yang memetakan sebuah nama ke alamat IPv4

###### nslookup

Perintah untuk mengecek apakah sebuah nama domain berhasil diterjemahkan oleh DNS

###### HTTP (Hypertext Transfer Protocol)

Protokol yang dipakai browser untuk meminta dan menerima halaman web dari Web Server

###### Web Server

Perangkat atau layanan yang menyimpan dan menyajikan halaman web ke client

# 
