# Pertemuan 3: Inter-VLAN Routing & Access Control List (ACL)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar dan cara kerja Inter-VLAN Routing.
- Praktikan mampu menjelaskan peran VLAN tagging (IEEE 802.1Q) dan trunk link dalam proses routing antar-VLAN.
- Praktikan mampu membedakan karakteristik metode Router-on-a-Stick (RoAS) dan Switch Virtual Interface (SVI).
- Praktikan mampu mengonfigurasi Inter-VLAN Routing menggunakan metode RoAS dan SVI pada perangkat Cisco.
- Praktikan mampu memahami konsep dasar dan cara kerja Access Control List (ACL), termasuk wildcard mask.
- Praktikan mampu membedakan karakteristik Standard ACL, Extended ACL, Numbered ACL, dan Named ACL beserta aturan penempatannya.
- Praktikan mampu mengonfigurasi Standard Numbered, Extended Numbered, Standard Named, dan Extended Named ACL pada router Cisco.
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
Router(config)# interface FastEthernet 0/0.10
```

> 💡 **Cara membaca prompt CLI Cisco** (supaya tidak bingung saat mengetik):
> | Prompt | Artinya | Cara masuk |
> |---|---|---|
> | `Router>` | User EXEC mode (akses terbatas) | Kondisi awal |
> | `Router#` | Privileged EXEC mode | ketik `enable` |
> | `Router(config)#` | Global configuration mode | ketik `configure terminal` |
> | `Router(config-if)#` | Konfigurasi interface | ketik `interface <nama>` |
> | `Router(config-subif)#` | Konfigurasi sub-interface | ketik `interface <nama>.<nomor>` |
> | `Router(config-ext-nacl)#` | Konfigurasi Extended Named ACL | ketik `ip access-list extended <nama>` |
> | `Router(config-std-nacl)#` | Konfigurasi Standard Named ACL | ketik `ip access-list standard <nama>` |
>
> Perintah `exit` untuk naik satu level, `end` untuk langsung kembali ke `#`.
> Baris yang diawali tanda `!` pada blok kode hanyalah **komentar** penjelas, tidak perlu diketik.

---

## 📖 Materi Praktikum

# Bagian A — Inter-VLAN Routing

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

> 💡 **Istilah singkat:**
> - **Port access** = port switch yang hanya membawa **satu** VLAN (biasanya ke PC).
> - **Port trunk** = port switch yang membawa **banyak** VLAN sekaligus (biasanya ke router atau switch lain).
> - **Default gateway** = "pintu keluar" sebuah VLAN. Setiap PC harus diarahkan ke gateway agar bisa berkomunikasi dengan VLAN lain.

### 3. Metode Inter-VLAN Routing

Ada dua metode utama yang umum digunakan untuk mengimplementasikan Inter-VLAN Routing pada jaringan masa kini, yaitu **Router-on-a-Stick (RoAS)** dan **Switch Virtual Interface (SVI)**. Keduanya memiliki karakteristik, kelebihan, serta keterbatasan yang berbeda, terutama dari sisi perangkat yang dibutuhkan, performa, dan skalabilitas.

#### 3.1 Router-on-a-Stick (RoAS)

RoAS adalah metode Inter-VLAN Routing yang hanya menggunakan satu interface fisik router, namun dipecah menjadi beberapa sub-interface logis (virtual). Setiap sub-interface diberi *encapsulation* 802.1Q dan dikonfigurasi sebagai *default gateway* untuk satu VLAN tertentu. Interface fisik router tersebut dihubungkan ke port trunk pada switch, sehingga satu kabel dapat membawa trafik banyak VLAN sekaligus.

**Karakteristik RoAS:**
- Menggunakan router eksternal (bukan switch) sebagai perangkat Layer 3.
- Satu interface fisik (misal `FastEthernet0/0`) dipecah menjadi sub-interface (`Fa0/0.10`, `Fa0/0.20`, dst).
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
R1(config)# interface FastEthernet 0/0
R1(config-if)# no shutdown
R1(config-if)# exit

! Sub-interface untuk VLAN 10
R1(config)# interface FastEthernet 0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0
R1(config-subif)# exit

! Sub-interface untuk VLAN 20
R1(config)# interface FastEthernet 0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0
R1(config-subif)# exit
```

> ⚠️ **Catatan:** Angka pada perintah `encapsulation dot1Q <id>` harus sama persis dengan VLAN ID yang diizinkan pada port trunk switch. Interface fisik utama (`Fa0/0`) tetap harus dalam kondisi `no shutdown` agar sub-interface dapat aktif.

> 💡 **Kebiasaan penamaan:** angka setelah titik pada sub-interface (`Fa0/0.10`) sebaiknya sama dengan VLAN ID agar mudah diingat. Secara teknis angka itu hanya label; yang menentukan VLAN adalah perintah `encapsulation dot1Q`.

**Konfigurasi IP pada PC:**

| Perangkat | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC1 (VLAN 10) | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 (VLAN 20) | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

#### 3.2 Switch Virtual Interface / Switch Multilayer (SVI)

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

---

# Bagian B — Access Control List (ACL)

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

### 6. Latihan Praktikum

**Latihan 1 — Inter-VLAN Routing (RoAS)**
1. Rancang sebuah topologi di Cisco Packet Tracer dimana terdapat 2 ruangan (Ruang Dosen dan Ruang Lab) yang masing-masing terdiri dari 2 PC dan terdapat 1 switch dan 1 router.
2. Buat VLAN 10 (Dosen) dan VLAN 20 (Lab) di switch.
3. Konfigurasikan Inter-VLAN Router-on-a-Stick pada router.
4. Lakukan validasi, apakah kedua ruangan tersebut bisa saling terhubung.

**Latihan 2 — ACL**
1. Tambahkan VLAN 30 (Server) dengan network 192.168.30.0/24 dan satu PC/server pada topologi Latihan 1.
2. Buat **Standard Numbered ACL** agar VLAN 10 (Dosen) tidak bisa mengakses VLAN 30, dengan VLAN 20 tetap bisa. Tentukan sendiri interface dan arah pemasangannya, lalu jelaskan alasannya.
3. Ulangi dengan **Extended Numbered ACL** agar VLAN 10 ↔ VLAN 30 terblokir dua arah dalam **satu** ACL.
4. Tulis ulang soal 2 dan 3 sebagai **Standard Named** dan **Extended Named ACL**.
5. Pada Extended Named ACL, sisipkan pengecualian agar VLAN 10 tetap bisa mengakses web server (port 80) di VLAN 30. Ingat: balasan juga perlu diizinkan.
6. Verifikasi dengan `show access-lists` dan ping dari PC. Catat jumlah *match(es)* tiap baris.

---

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer — Inter-VLAN Routing (RoAS & SVI) dan Access Control List (Standard, Extended, Named ACL)
