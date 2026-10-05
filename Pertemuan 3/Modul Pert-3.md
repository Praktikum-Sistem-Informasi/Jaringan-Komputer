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



### 6. Latihan Praktikum

**Latihan 1 — Inter-VLAN Routing (RoAS)**
1. Rancang sebuah topologi di Cisco Packet Tracer dimana terdapat 2 ruangan (Ruang Dosen dan Ruang Lab) yang masing-masing terdiri dari 2 PC dan terdapat 1 switch dan 1 router.
2. Buat VLAN 10 (Dosen) dan VLAN 20 (Lab) di switch.
3. Konfigurasikan Inter-VLAN Router-on-a-Stick pada router.
4. Lakukan validasi, apakah kedua ruangan tersebut bisa saling terhubung.


---

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer — Inter-VLAN Routing (RoAS & SVI) dan Access Control List (Standard, Extended, Named ACL)
