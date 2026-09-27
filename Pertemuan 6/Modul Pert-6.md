# Pertemuan 6: DHCP, DNS, dan Web Server (Disesuaikan dengan Topologi Praktik)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar DHCP dan proses DORA (Discover, Offer, Request, Acknowledge) dalam pemberian alamat IP secara otomatis.
- Praktikan mampu mengonfigurasi DHCP server bawaan (IOS) pada perangkat Router untuk segmen VLAN kecil (VLAN 40).
- Praktikan mampu mengonfigurasi layanan DHCP pada perangkat Server untuk segmen VLAN besar (VLAN 50) yang berada di luar broadcast domain server tersebut.
- Praktikan mampu mengonfigurasi **inter-VLAN routing (router-on-a-stick)** menggunakan subinterface dan trunk, sebagai prasyarat DHCP untuk VLAN yang lewat trunk.
- Praktikan mampu mengonfigurasi **`ip helper-address`** agar broadcast DHCP dari client dapat diteruskan (relay) ke DHCP server yang berada di segmen berbeda.
- Praktikan mampu mengonfigurasi topologi yang menggabungkan DHCP router dan DHCP server secara bersamaan tanpa terjadi konflik scope.
- Praktikan mampu menjelaskan alasan penggunaan DHCP server (bukan hanya router) untuk kebutuhan jaringan berskala besar.
- Praktikan mampu memahami konsep DNS dan mengonfigurasi resource record pada DNS server agar sebuah domain dapat diakses menggunakan nama, bukan alamat IP.
- Praktikan mampu memahami konsep dasar Web Server dan mengonfigurasi layanan HTTP pada perangkat Server di Cisco Packet Tracer.
- Praktikan mampu menghubungkan DHCP, DNS, dan Web Server dalam satu alur kerja: client mendapat IP dari DHCP (lewat relay), mengetik nama domain di browser, domain tersebut diterjemahkan oleh DNS, lalu halaman web diambil dari Web Server.
- Praktikan mampu melakukan verifikasi konfigurasi menggunakan perintah `show ip dhcp binding`, `show ip interface brief`, `show interfaces trunk`, `nslookup`, dan pengujian akses lewat Web Browser pada PC.

## 🗺️ Gambaran Topologi
- **VLAN 40 – Administrasi** (`192.168.40.0/28`): DHCP dari **Router0** (IOS DHCP Server), segmen kecil/lokal, access port biasa.
- **VLAN 50 – HRD** (`192.168.50.0/28`, gateway `192.168.50.14`): DHCP dari **Server0**, dijangkau via **inter-VLAN routing (router-on-a-stick)** di Router1 karena koneksi Router1–Switch2 adalah **trunk**.
- **Server0** berada di segmen terpisah `192.168.100.0/29` (gateway `192.168.100.1` di Fa1/1 Router1), menjalankan service **DHCP, DNS, dan HTTP** sekaligus.
- Karena Server0 tidak berada satu segmen fisik dengan VLAN 50, dibutuhkan **`ip helper-address`** di subinterface Router1 agar request DHCP dari PC VLAN 50 bisa diteruskan ke Server0.

## 📖 Materi Praktikum

### 1. Konsep Dasar DHCP
DHCP (Dynamic Host Configuration Protocol) memberikan konfigurasi IP address, subnet mask, default gateway, dan DNS secara otomatis kepada client. Proses ini dikenal sebagai **DORA**:

- **Discover** – Client broadcast mencari DHCP server.
- **Offer** – Server menawarkan satu IP yang tersedia.
- **Request** – Client meminta resmi IP yang ditawarkan.
- **Acknowledge** – Server mengonfirmasi, client resmi memakai IP tersebut (lease time).

Pada praktikum ini, DHCP dikonfigurasi dengan **kombinasi Router (VLAN 40) dan Server (VLAN 50)** dalam satu topologi — dan khusus VLAN 50, ditambah **relay DHCP** karena server tidak nempel langsung di segmen tersebut.

### 2. Konfigurasi DHCP pada Router (VLAN 40)

Cocok untuk segmen kecil/lokal, diaktifkan langsung lewat CLI tanpa perangkat tambahan.

**Langkah kerja (Router0):**
```
Router0(config)# interface FastEthernet0/1
Router0(config-if)# ip address 192.168.40.1 255.255.255.240
Router0(config-if)# no shutdown
Router0(config-if)# exit

Router0(config)# ip dhcp excluded-address 192.168.40.1 192.168.40.2
Router0(config)# ip dhcp pool VLAN40_ADMIN
Router0(dhcp-config)# network 192.168.40.0 255.255.255.240
Router0(dhcp-config)# default-router 192.168.40.1
Router0(dhcp-config)# dns-server 192.168.100.2
Router0(dhcp-config)# exit
```

**Uji Verifikasi:** Set PC di VLAN 40 ke DHCP → `ipconfig` → cek IP sesuai pool. Jalankan `Router0# show ip dhcp binding` untuk memastikan IP client tercatat di router.

### 3. Inter-VLAN Routing (Router-on-a-Stick) untuk VLAN 50

Karena koneksi Router1–Switch2 adalah **trunk** (bukan access), IP gateway VLAN 50 **tidak** dipasang langsung di interface fisik, melainkan di **subinterface**.

**Langkah kerja (Router1):**
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
```

**Wajib dicek di Switch2** — port yang mengarah ke Router1 harus trunk:
```
Switch2(config)# interface FastEthernet0/1
Switch2(config-if)# switchport mode trunk
```
Verifikasi: `Switch2# show interfaces trunk` harus menampilkan port tersebut sebagai trunk. Tanpa ini, subinterface `.50` tidak akan pernah menerima frame apa pun dari VLAN 50.

> ⚠️ Perhitungan subnet `/28` untuk `192.168.50.0/28`: Network `192.168.50.0`, host valid `192.168.50.1`–`192.168.50.14`, broadcast `192.168.50.15`. Karena gateway memakai `.14`, sisa host yang bisa dibagikan DHCP adalah `.1`–`.13` (13 alamat).

### 4. `ip helper-address` — Kunci Relay DHCP Lintas Segmen

Broadcast DHCP Discover **tidak bisa** melewati router secara default. Karena Server0 berada di segmen lain (`192.168.100.0/29`), broadcast dari PC VLAN 50 akan mentok di Router1 kecuali diberi tahu ke mana harus diteruskan:

```
Router1(config-subif)# ip helper-address 192.168.100.2
```

Perintah ini membuat Router1 mengubah broadcast DHCP menjadi unicast dan mengirimkannya langsung ke `192.168.100.2` (Server0). Alur DORA-nya jadi:

1. PC broadcast Discover di VLAN 50
2. Router1 (di `Fa0/0.50`) merelay ke Server0 (`192.168.100.2`)
3. Server0 balas Offer → diteruskan balik ke PC lewat Router1
4. Request → Acknowledge dengan mekanisme relay yang sama

> ⚠️ Jika `ip helper-address` tidak ada/salah alamat, service DHCP di server bisa saja sudah aktif dan pool sudah benar, tapi PC tetap mendapat APIPA (`169.254.x.x`) karena request tidak pernah sampai ke server.

### 5. Konfigurasi DHCP pada Server (VLAN 50)

**Langkah kerja pada Server0:**

1. Tab **Desktop → IP Configuration** → set **Static**:
   - IP Address: `192.168.100.2`
   - Subnet Mask: `255.255.255.248`
   - Default Gateway: `192.168.100.1`

2. Tab **Config/Services → DHCP**:
   - Service: **On**
   - Default Gateway: `192.168.50.14`
   - DNS Server: `192.168.100.2` (IP Server0 sendiri, **bukan** IP router)
   - Start IP Address: `192.168.50.1`
   - Subnet Mask: `255.255.255.240`
   - Maximum Number of Users: `13`
   - Klik **Add** (jika pool baru) lalu **Save**

**Uji Verifikasi:** Set PC di VLAN 50 ke DHCP → `ipconfig` → harus dapat IP dari rentang `.1`–`.13`, bukan APIPA. Cek juga daftar client di panel DHCP Server0 (bukan `show ip dhcp binding`, karena binding-nya tercatat di **server**, bukan router).

### 6. Kombinasi DHCP Router dan Server dalam Satu Topologi

Scope router (VLAN 40) dan scope server (VLAN 50) **harus berbeda network**, tidak boleh tumpang tindih.

| Sumber DHCP | Segmen | Network | Rentang yang dibagikan |
|---|---|---|---|
| Router0 | VLAN 40 – Administrasi | 192.168.40.0/28 | 192.168.40.3 – 192.168.40.14 |
| Server0 | VLAN 50 – HRD | 192.168.50.0/28 | 192.168.50.1 – 192.168.50.13 |

**Uji Verifikasi Gabungan:** Set PC di kedua VLAN ke DHCP, pastikan masing-masing dapat IP dari sumber yang seharusnya, dan tidak ada duplikasi IP antar segmen.

### 7. Alasan Penggunaan DHCP Server

- Menangani volume permintaan IP lebih banyak dan stabil dibanding fitur DHCP bawaan router.
- Manajemen terpusat, administrasi dan logging lebih mudah dari satu titik.
- Lebih mudah diskalakan tanpa membebani performa router.
- Dalam topologi ini, DHCP server juga sekaligus menjalankan DNS dan HTTP — mencontohkan bagaimana satu server bisa melayani banyak fungsi untuk segmen besar.

### 8. Konsep dan Konfigurasi DNS

**Langkah kerja pada Server0 (Config/Services → DNS):**
1. DNS Service: **On**
2. Isi Resource Record:
   - Type: `A Record`
   - Name: `web-praktisi.local`
   - Address: `192.168.100.2`
3. Klik **Add** dulu (memasukkan ke tabel), baru **Save**
4. Pastikan record muncul di tabel (kolom No./Name/Type/Detail terisi) — kalau tabel masih kosong, record belum tersimpan dan `nslookup` akan tetap gagal.

**Langkah kerja pada Client:**
Field DNS Server pada PC akan otomatis terisi `192.168.100.2` kalau IP didapat lewat DHCP yang sudah dikonfigurasi dengan `dns-server 192.168.100.2` (untuk VLAN 40 lewat router) atau field DNS Server di pool (untuk VLAN 50 lewat server).

**Uji Verifikasi:** `nslookup web-praktisi.local` di Command Prompt PC harus resolve ke `192.168.100.2`.

### 9. Konsep dan Konfigurasi Web Server

**Langkah kerja pada Server0 (Config/Services → HTTP):**
1. HTTP: **On**
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
3. **Save**

**Uji Verifikasi (urutan disarankan):**
1. Buka Web Browser di PC → ketik `http://192.168.100.2` dulu (pastikan HTTP jalan lepas dari DNS)
2. Kalau berhasil, lanjut ketik `http://web-praktisi.local` (buktikan DNS + HTTP nyambung)

> ⚠️ Kalau (1) gagal: cek PC sudah dapat IP dari DHCP (bukan APIPA), dan bisa `ping 192.168.100.2` — kalau ping gagal, masalah di routing/`ip helper-address`, bukan di HTTP.
> Kalau (1) berhasil tapi (2) gagal: cek record DNS sudah ter-*Add* dan ter-*Save*, serta field DNS Server di pool DHCP sudah benar (`192.168.100.2`), lalu renew DHCP di PC.

## ✅ Ringkasan Urutan Konfigurasi
1. Router0: IP interface + DHCP pool untuk VLAN 40
2. Switch2: set port ke Router1 jadi **trunk**
3. Router1: subinterface `Fa0/0.50` (encapsulation dot1Q 50) + `ip helper-address` ke Server0
4. Router1: interface `Fa1/1` ke Server0 (`192.168.100.1/29`)
5. Server0: Static IP, lalu aktifkan DHCP (untuk VLAN 50), DNS, dan HTTP
6. Uji: DHCP binding (router untuk VLAN 40, panel server untuk VLAN 50) → `nslookup` → Web Browser

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Cisco. *IP Addressing: DHCP Configuration Guide, Configuring the Cisco IOS DHCP Server*. [cisco.com](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/15-mt/dhcp-15-mt-book/config-dhcp-server.html)
- Cisco. *Configuring DHCP Relay (ip helper-address)*. [cisco.com](https://www.cisco.com/c/en/us/support/docs/ip/dynamic-address-allocation-resolution/13725-30.html)
- Droms, R. *RFC 2131, Dynamic Host Configuration Protocol*. IETF. [datatracker.ietf.org/doc/html/rfc2131](https://datatracker.ietf.org/doc/html/rfc2131)
- Mockapetris, P. *RFC 1035, Domain Names, Implementation and Specification*. IETF. [datatracker.ietf.org/doc/html/rfc1035](https://datatracker.ietf.org/doc/html/rfc1035)
- Fielding, R., dan Reschke, J. *RFC 7230, Hypertext Transfer Protocol (HTTP/1.1): Message Syntax and Routing*. IETF. [datatracker.ietf.org/doc/html/rfc7230](https://datatracker.ietf.org/doc/html/rfc7230)
