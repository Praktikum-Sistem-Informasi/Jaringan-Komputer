# Pertemuan 3: Inter-VLAN Routing & Access Control List (ACL)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar dan cara kerja Inter-VLAN Routing.
- Praktikan mampu menjelaskan peran VLAN tagging (IEEE 802.1Q) dan trunk link dalam proses routing antar-VLAN.
- Praktikan mampu membedakan karakteristik metode Router-on-a-Stick (RoAS) dan Switch Virtual Interface (SVI).
- Praktikan mampu mengonfigurasi Inter-VLAN Routing menggunakan metode RoAS dan SVI pada perangkat Cisco.
- Praktikan mampu memahami konsep dasar dan cara kerja Access Control List (ACL), termasuk wildcard mask.
- Praktikan mampu membedakan karakteristik Standard ACL, Extended ACL, dan Named ACL beserta aturan penempatannya.
- Praktikan mampu mengonfigurasi Standard, Extended, dan Named ACL pada router Cisco.
- Praktikan mampu melakukan verifikasi dan troubleshooting konfigurasi Inter-VLAN Routing maupun ACL.

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: rancangan topologi router + switch + multi-subnet, serta instruksi konfigurasi RoAS/SVI dan Standard/Extended/Named ACL.
- `docs/` — berisi modul ini beserta materi pendukung lain (cheatsheet CLI, referensi VLAN/ACL).

## 🚀 Cara Menjalankan
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
# 1. Buka file topologi pada Cisco Packet Tracer (src/topologi.pkt)
# 2. Klik perangkat Router/Switch, buka tab CLI, lalu masukkan perintah konfigurasi, contoh:
Router> enable
Router# configure terminal
Router(config)# interface gigabitEthernet 0/0.10
```

## 📖 Materi Praktikum

# Inter-VLAN Routing

### 1. Pengertian Inter-VLAN Routing

Inter-VLAN Routing adalah proses routing yang dijalankan oleh router agar masing-masing komputer pada VLAN yang berbeda bisa saling berhubungan. VLAN diasosiasikan dengan IP subnet yang unik pada network, sehingga konfigurasi subnet akan memfasilitasi proses routing pada lingkungan beberapa VLAN. Tujuan utama Inter-VLAN Routing adalah meneruskan trafik antar-VLAN, yaitu menghubungkan dua buah VLAN yang berbeda ID-nya.

Sebagaimana diketahui, VLAN membagi sebuah jaringan menjadi beberapa segmen dan broadcast domain, di mana paket data tidak akan diteruskan ke VLAN yang bukan tujuannya. Untuk dapat menghubungkan antar-VLAN, dibutuhkan perangkat yang memiliki kapasitas untuk melakukan routing, yaitu router atau switch Layer 3.

> **Ilustrasi:** Bayangkan sebuah gedung kantor dengan tiga departemen — Keuangan, Operasional, dan IT — yang masing-masing berada di VLAN terpisah. Komputer di departemen Keuangan dan komputer di departemen Operasional tidak bisa langsung bertukar data karena berada di broadcast domain yang berbeda. Agar keduanya bisa berkomunikasi saat diperlukan, misalnya mengakses server bersama, dibutuhkan mekanisme routing di antara kedua VLAN tersebut.

### 2. Cara Kerja Inter-VLAN Routing

Ketika sebuah perangkat di VLAN 10 ingin mengirim data ke perangkat di VLAN 20, alurnya adalah sebagai berikut:

1. Perangkat pengirim di VLAN 10 mengirimkan paket ke *default gateway* milik VLAN 10 — ini adalah alamat IP yang dikonfigurasi di router atau switch Layer 3.
2. Paket masuk ke perangkat Layer 3. Perangkat ini memeriksa tabel routing untuk menentukan jalur terbaik ke subnet tujuan (VLAN 20).
3. Perangkat Layer 3 meneruskan paket ke interface atau sub-interface yang terhubung ke VLAN 20.
4. Paket sampai ke perangkat tujuan di VLAN 20.

Yang membedakan Inter-VLAN Routing dari routing biasa antar-jaringan WAN adalah skala dan konteksnya: prosesnya terjadi di dalam satu gedung atau satu infrastruktur lokal yang sama, menggunakan VLAN tagging (**IEEE 802.1Q**) sebagai cara membedakan lalu lintas dari berbagai VLAN.

Standar 802.1Q memungkinkan satu koneksi fisik membawa lalu lintas dari banyak VLAN sekaligus — inilah yang disebut **trunk link**. Frame yang melewati trunk link diberi tag berupa VLAN ID, sehingga perangkat penerima tahu frame tersebut berasal dari VLAN berapa. Pemahaman tentang trunk link ini penting karena kedua metode Inter-VLAN Routing yang dibahas pada modul ini bergantung padanya.

### 3. Metode Inter-VLAN Routing

Ada dua metode utama yang umum digunakan untuk mengimplementasikan Inter-VLAN Routing pada jaringan masa kini, yaitu **Router-on-a-Stick (RoAS)** dan **Switch Virtual Interface (SVI)**. Keduanya memiliki karakteristik, kelebihan, serta keterbatasan yang berbeda, terutama dari sisi perangkat yang dibutuhkan, performa, dan skalabilitas.

#### 3.1 Router-on-a-Stick (RoAS)

RoAS adalah metode Inter-VLAN Routing yang hanya menggunakan satu interface fisik router, namun dipecah menjadi beberapa sub-interface logis (virtual). Setiap sub-interface diberi *encapsulation* 802.1Q dan dikonfigurasi sebagai *default gateway* untuk satu VLAN tertentu. Interface fisik router tersebut dihubungkan ke port trunk pada switch, sehingga satu kabel dapat membawa trafik banyak VLAN sekaligus.

**Karakteristik RoAS:**
- Menggunakan router eksternal (bukan switch) sebagai perangkat Layer 3.
- Satu interface fisik (misal `GigabitEthernet0/0`) dipecah menjadi sub-interface (`G0/0.10`, `G0/0.20`, dst).
- Port switch yang terhubung ke router dikonfigurasi sebagai trunk.
- Cocok untuk jaringan kecil-menengah, atau ketika hanya tersedia router (tanpa switch Layer 3).
- Kekurangan: satu link fisik menjadi titik kemacetan (*bottleneck*) karena seluruh trafik antar-VLAN melewati satu kabel yang sama.

**Konfigurasi RoAS** *(dilakukan pada dua perangkat: Switch dan Router)*:

```
! ===== 1. Membuat VLAN pada Switch =====
SW1> enable
SW1# configure terminal
SW1(config)# vlan 10
SW1(config-vlan)# name Finance
SW1(config-vlan)# exit
SW1(config)# vlan 20
SW1(config-vlan)# name Sales
SW1(config-vlan)# exit

! ===== 2. Konfigurasi Port Access ke PC =====
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20
SW1(config-if)# exit

! ===== 3. Konfigurasi Port Trunk ke Router =====
SW1(config)# interface fastEthernet 0/3
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20
SW1(config-if)# exit
```

```
! ===== 4. Konfigurasi Sub-interface pada Router =====
R1> enable
R1# configure terminal
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# no shutdown
R1(config-if)# exit

! Sub-interface untuk VLAN 10
R1(config)# interface gigabitEthernet 0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0
R1(config-subif)# exit

! Sub-interface untuk VLAN 20
R1(config)# interface gigabitEthernet 0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0
R1(config-subif)# exit
```

> ⚠️ **Catatan:** Angka pada perintah `encapsulation dot1Q <id>` harus sama persis dengan VLAN ID yang diizinkan pada port trunk switch. Interface fisik utama (`G0/0`) tetap harus dalam kondisi `no shutdown` agar sub-interface dapat aktif.

**Konfigurasi IP pada PC:**

| Perangkat | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC1 (VLAN 10) | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 (VLAN 20) | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

#### 3.2 Switch Virtual Interface (SVI)

SVI adalah interface virtual pada switch Layer 3 (*multilayer switch*) yang mewakili sebuah VLAN dalam bentuk logis. Setiap VLAN dapat memiliki satu SVI yang berfungsi sebagai *default gateway*. Routing antar-VLAN dilakukan sepenuhnya di dalam switch itu sendiri (di ASIC), tanpa memerlukan router eksternal.

**Karakteristik SVI:**
- Memerlukan switch Layer 3 (*multilayer switch*), misalnya Cisco Catalyst 3560/3650/3750 ke atas.
- Fitur `ip routing` harus diaktifkan secara global pada switch.
- Setiap VLAN memiliki interface virtual (`interface vlan <id>`) yang diberi alamat IP sebagai gateway.
- Tidak memerlukan trunk khusus ke router karena routing terjadi langsung di switch (kecuali jika tetap butuh trunk antar-switch).
- Performa lebih baik dibanding RoAS karena proses routing dilakukan dengan hardware switching (*line-rate*), bukan melalui satu link fisik yang sama.

**Konfigurasi SVI** *(dilakukan pada satu perangkat saja: switch Layer 3, tanpa router eksternal)*:

```
! ===== 1. Membuat VLAN =====
SW1> enable
SW1# configure terminal
SW1(config)# vlan 10
SW1(config-vlan)# name Finance
SW1(config-vlan)# exit
SW1(config)# vlan 20
SW1(config-vlan)# name Sales
SW1(config-vlan)# exit

! ===== 2. Konfigurasi Port Access ke PC =====
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# exit

SW1(config)# interface fastEthernet 0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20
SW1(config-if)# exit

! ===== 3. Mengaktifkan IP Routing =====
SW1(config)# ip routing

! ===== 4. Membuat dan Mengaktifkan SVI =====
SW1(config)# interface vlan 10
SW1(config-if)# ip address 192.168.10.1 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit

SW1(config)# interface vlan 20
SW1(config-if)# ip address 192.168.20.1 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit
```

> ⚠️ **Catatan:** Perintah `interface vlan <id>` hanya akan aktif (*up/up*) bila VLAN tersebut sudah dibuat dan memiliki minimal satu port access yang statusnya *up* pada VLAN itu.

**Konfigurasi IP pada PC:**

Sama seperti pada metode RoAS, PC1 dan PC2 dikonfigurasi dengan IP address, subnet mask, dan default gateway sesuai VLAN masing-masing (lihat tabel pada bagian 3.1).

#### 3.3 Perbandingan RoAS vs SVI

| Aspek | RoAS | SVI |
|---|---|---|
| Perangkat | Router + Switch Layer 2 biasa | Switch Layer 3 (*multilayer switch*) |
| Performa | Terbatas oleh bandwidth 1 link fisik | Lebih tinggi (*line-rate*, hardware switching) |
| Skalabilitas | Kurang ideal untuk VLAN banyak/trafik tinggi | Lebih baik untuk jaringan besar |
| Biaya perangkat | Relatif lebih murah | Lebih mahal |
| Cocok untuk | Jaringan kecil, lab, cabang kecil | Jaringan menengah-besar, kampus, kantor pusat |

### 4. Verifikasi Inter-VLAN Routing

| Perintah | Fungsi |
|---|---|
| `show vlan brief` | Menampilkan daftar VLAN dan port anggotanya |
| `show interfaces trunk` | Menampilkan status dan VLAN yang diizinkan pada port trunk |
| `show ip interface brief` | Menampilkan status IP dan up/down setiap interface / SVI |
| `show ip route` | Menampilkan tabel routing (*connected routes* antar-VLAN) |
| `ping <ip tujuan>` | Menguji konektivitas antar-host/antar-VLAN |

**Uji Verifikasi:** Lakukan tes ping dari PC1 (VLAN 10) ke PC2 (VLAN 20) dan sebaliknya. Jika Inter-VLAN Routing berhasil dikonfigurasi (baik dengan RoAS maupun SVI), hasil ping harus *Reply*, bukan lagi *Request Timed Out* (RTO).

# Access Control List (ACL)

### 1. Pengertian ACL

Access Control List (ACL) adalah kumpulan aturan berurutan (*sequential statements*) yang dikonfigurasi pada router atau switch Layer 3 untuk mengizinkan (*permit*) atau menolak (*deny*) paket data yang melewati sebuah interface, berdasarkan kriteria seperti alamat IP sumber/tujuan, jenis protokol (TCP, UDP, ICMP, dan lain-lain), serta nomor port.

ACL bekerja layaknya seorang penjaga gerbang: setiap paket yang melewati interface yang dipasangi ACL akan dicocokkan dengan daftar aturan yang telah dibuat, kemudian diizinkan atau ditolak berdasarkan aturan yang paling pertama cocok.

> **Ilustrasi:** Melanjutkan topologi Bagian A, misalkan perusahaan tidak ingin VLAN Sales (VLAN 20) bisa mengakses server data keuangan sama sekali, sementara VLAN Finance (VLAN 10) boleh mengaksesnya. Setelah Inter-VLAN Routing aktif, semua VLAN sebenarnya sudah bisa saling terhubung — ACL-lah yang memberi router kemampuan untuk secara selektif menyaring trafik semacam ini.

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

> ⚠️ **Implicit Deny:** Setiap ACL Cisco selalu diakhiri dengan aturan `deny any` yang tidak terlihat pada konfigurasi. Artinya, jika sebuah ACL hanya berisi aturan *permit* tanpa ada satu pun *deny* eksplisit, seluruh trafik yang tidak memenuhi kriteria *permit* tersebut akan otomatis ditolak.

**Wildcard Mask:** ACL Cisco tidak menggunakan subnet mask biasa, melainkan *wildcard mask* untuk menentukan rentang IP yang dicocokkan. Prinsipnya berkebalikan dengan subnet mask: bit `0` berarti oktet harus cocok persis, bit `1` berarti oktet boleh bernilai apa saja (diabaikan).

| Kebutuhan | Subnet Mask | Wildcard Mask |
|---|---|---|
| Host tunggal (192.168.10.5) | 255.255.255.255 | 0.0.0.0 |
| Subnet /24 (192.168.10.0) | 255.255.255.0 | 0.0.0.255 |
| Subnet /16 (172.16.0.0) | 255.255.0.0 | 0.0.255.255 |
| Semua alamat (any) | 0.0.0.0 | 255.255.255.255 |

Cara cepat menghitung wildcard mask: kurangi setiap oktet subnet mask dari 255. Contoh: `255.255.255.192` → wildcard `0.0.0.63` (255−192=63). Kata kunci `any` adalah singkatan dari `0.0.0.0 255.255.255.255`, sedangkan `host <ip>` adalah singkatan dari `<ip> 0.0.0.0`.

### 3. Jenis-Jenis ACL

Ada tiga jenis utama ACL pada Cisco IOS, dibedakan dari kriteria penyaringan dan cara identifikasinya: **Standard ACL**, **Extended ACL**, dan **Named ACL**.

#### 3.1 Standard ACL

Standard ACL hanya menyaring paket berdasarkan alamat IP **sumber** saja, diidentifikasi dengan nomor **1–99** atau **1300–1999** (*expanded*).

**Karakteristik:**
- Hanya mengenali IP sumber, tidak bisa berdasarkan IP tujuan, protokol, atau port.
- Karena hanya mengenali IP sumber, sebaiknya diterapkan **sedekat mungkin dengan tujuan (destination)**.
- Cocok untuk kebutuhan filtering sederhana, misalnya memblokir satu subnet agar tidak bisa mengakses jaringan lain sama sekali.

**Konfigurasi Standard ACL** — *Skenario: blokir seluruh trafik dari VLAN 20 (Sales, 192.168.20.0/24) menuju subnet Server (192.168.30.0/24), subnet lain tetap diizinkan.*

```
! ===== 1. Membuat ACL =====
R1> enable
R1# configure terminal
R1(config)# access-list 10 deny 192.168.20.0 0.0.0.255
R1(config)# access-list 10 permit any

! ===== 2. Menerapkan ACL pada interface (dekat tujuan, arah outbound) =====
R1(config)# interface gigabitEthernet 0/2
R1(config-if)# ip access-group 10 out
R1(config-if)# exit
```

> ⚠️ **Catatan:** Baris `permit any` wajib ditambahkan secara eksplisit. Tanpa baris ini, seluruh trafik lain (termasuk dari VLAN Finance) ikut ditolak karena *implicit deny*.

**Verifikasi:**
```
R1# show access-lists 10
Standard IP access list 10
    10 deny 192.168.20.0, wildcard bits 0.0.0.255
    20 permit any
```

#### 3.2 Extended ACL

Extended ACL menyaring berdasarkan kombinasi IP sumber, IP **tujuan**, protokol (TCP/UDP/ICMP/IP), serta nomor port. Diidentifikasi dengan nomor **100–199** atau **2000–2699** (*expanded*).

**Karakteristik:**
- Dapat memfilter berdasarkan IP sumber DAN tujuan, protokol, serta port.
- Karena sudah mengenali IP tujuan, sebaiknya diterapkan **sedekat mungkin dengan sumber (source)**.
- Lebih fleksibel namun lebih kompleks dibanding Standard ACL.

**Konfigurasi Extended ACL** — *Skenario: PC1 (192.168.10.10, VLAN Finance) hanya boleh mengakses Server1 (192.168.30.10) via HTTP (port 80); trafik lain dari PC1 ke Server1 ditolak; tujuan lain tetap diizinkan.*

```
! ===== 1. Membuat ACL =====
R1> enable
R1# configure terminal
R1(config)# access-list 110 permit tcp host 192.168.10.10 host 192.168.30.10 eq 80
R1(config)# access-list 110 deny ip host 192.168.10.10 host 192.168.30.10
R1(config)# access-list 110 permit ip any any

! ===== 2. Menerapkan ACL pada interface (dekat sumber, arah inbound) =====
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip access-group 110 in
R1(config-if)# exit
```

**Verifikasi:**
```
R1# show access-lists 110
Extended IP access list 110
    10 permit tcp host 192.168.10.10 host 192.168.30.10 eq www
    20 deny ip host 192.168.10.10 host 192.168.30.10
    30 permit ip any any
```

#### 3.3 Named ACL

Named ACL pada dasarnya adalah Standard/Extended ACL yang diberi **nama** (bukan nomor) sebagai identitasnya, sehingga lebih mudah dibaca dan dikelola.

**Karakteristik:**
- Menggunakan nama deskriptif (misal `ONLY_HTTP_TO_SERVER`) alih-alih nomor.
- Mendukung penghapusan/penyisipan satu baris (ACE) tertentu tanpa menghapus seluruh ACL, menggunakan nomor *sequence*.
- Tetap harus dideklarasikan sebagai `standard` atau `extended` saat pembuatan.

**Konfigurasi Named ACL** — *skenario sama seperti 7.2, ditulis ulang dengan nama:*

```
! ===== 1. Membuat Named ACL =====
R1> enable
R1# configure terminal
R1(config)# ip access-list extended ONLY_HTTP_TO_SERVER
R1(config-ext-nacl)# permit tcp host 192.168.10.10 host 192.168.30.10 eq 80
R1(config-ext-nacl)# deny ip host 192.168.10.10 host 192.168.30.10
R1(config-ext-nacl)# permit ip any any
R1(config-ext-nacl)# exit

! ===== 2. Menerapkan pada interface =====
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip access-group ONLY_HTTP_TO_SERVER in
R1(config-if)# exit
```

**Menyisipkan/menghapus baris tertentu:**
```
R1(config)# ip access-list extended ONLY_HTTP_TO_SERVER
! Menyisipkan aturan baru di antara sequence 10 dan 20
R1(config-ext-nacl)# 15 permit tcp host 192.168.10.10 host 192.168.30.10 eq 443
! Menghapus baris sequence 20 tanpa menghapus ACL secara keseluruhan
R1(config-ext-nacl)# no 20
R1(config-ext-nacl)# exit
```

#### 3.4 Perbandingan Jenis ACL

| Aspek | Standard ACL | Extended ACL | Named ACL |
|---|---|---|---|
| Kriteria filter | IP sumber saja | IP sumber, tujuan, protokol, port | Sama seperti Standard/Extended |
| Identifikasi | Nomor 1–99 / 1300–1999 | Nomor 100–199 / 2000–2699 | Nama (teks) |
| Penempatan ideal | Dekat tujuan (destination) | Dekat sumber (source) | Mengikuti jenis dasarnya |
| Edit per baris | Tidak | Tidak | Ya (sequence number) |

### 4. Aturan Penempatan ACL

- **Inbound ACL** — paket diperiksa ACL sebelum diproses tabel routing; lebih efisien jika paket memang akan ditolak.
- **Outbound ACL** — paket diproses routing dulu, baru diperiksa ACL sebelum keluar interface.
- **Standard ACL** → letakkan dekat **tujuan**, karena tidak mengenali IP tujuan sehingga berisiko memblokir trafik yang seharusnya masih boleh lewat jika diletakkan terlalu dekat sumber.
- **Extended ACL** → letakkan dekat **sumber**, karena sudah mengenali IP tujuan & protokol, sehingga paket yang seharusnya ditolak bisa langsung dibuang lebih awal.
- Setiap interface hanya boleh punya maksimal **satu ACL per arah, per protokol**.

### 5. Verifikasi ACL

| Perintah | Fungsi |
|---|---|
| `show access-lists` | Menampilkan semua ACL beserta jumlah paket yang cocok (*match*) tiap baris |
| `show access-lists <nomor/nama>` | Menampilkan detail satu ACL tertentu |
| `show ip interface <interface>` | Menampilkan ACL yang diterapkan pada suatu interface beserta arahnya |
| `show running-config` | Menampilkan konfigurasi ACL secara lengkap sesuai urutan baris |
| `ping` / `traceroute` | Menguji apakah trafik benar-benar diizinkan atau ditolak sesuai ACL |

**Masalah umum:**

| Gejala | Penyebab | Solusi |
|---|---|---|
| Semua trafik terblokir | Lupa `permit any` di akhir ACL | Tambahkan baris permit eksplisit sebelum implicit deny |
| ACL tidak berefek | Belum ada `ip access-group` di interface | Terapkan ACL pada interface dengan arah in/out yang benar |
| Trafik yang seharusnya diizinkan malah ditolak | Urutan ACE salah | Susun ulang dari yang paling spesifik ke paling umum |
| Trafik sumber lain ikut terblokir | Standard ACL diterapkan terlalu dekat sumber | Pindahkan ke interface dekat destination |

### 6. Tugas & Evaluasi Praktikum



## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer — Inter-VLAN Routing (RoAS & SVI) dan Access Control List (Standard, Extended, Named ACL)
