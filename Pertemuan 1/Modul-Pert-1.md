# Pertemuan 1: Pengenalan IP Address, Topologi Jaringan, dan Subnetting

## 🎯 Tujuan Pembelajaran
- Praktikan mampu menjelaskan pengertian, fungsi, dan struktur IP Address (IPv4).
- Praktikan mampu membedakan Network Address, Host Address, Broadcast Address, dan Subnet Mask.
- Praktikan mampu menjelaskan notasi CIDR serta membedakan Private IP Address dan Public IP Address.
- Praktikan mampu mengidentifikasi jenis-jenis topologi jaringan (Bus, Star, Ring, Mesh, Tree) beserta kelebihan dan kekurangannya.
- Praktikan mampu memahami konsep dasar subnetting, termasuk hubungan CIDR dengan jumlah subnet dan jumlah host.
- Praktikan mampu melakukan perhitungan subnetting IPv4 secara manual (Network Address, Broadcast Address, Host Range, dan Subnet Mask).

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi soal latihan perhitungan subnetting dan studi kasus pemilihan topologi jaringan.
- `docs/` — berisi modul ini beserta materi pendukung lain (tabel CIDR, cheatsheet perhitungan subnetting).

## 🚀 Cara Menjalankan
Praktikum pertemuan ini bersifat konseptual dan perhitungan manual, tidak menggunakan bahasa pemrograman. Alur pengerjaannya:
```
# 1. Baca dan pahami materi pada Pertemuan 1/Modul_Pert-1.md
# 2. Kerjakan soal latihan perhitungan subnetting secara manual di kertas/teks
```

## 📖 Materi Praktikum

### 1. Pengenalan IP Address

**Pengertian IP Address**

IP Address (Internet Protocol Address) adalah alamat numerik unik yang diberikan kepada setiap perangkat yang terhubung ke dalam suatu jaringan komputer, baik jaringan lokal (LAN) maupun jaringan luas seperti internet. Layaknya alamat rumah pada sistem pengiriman surat, setiap perangkat harus memiliki alamat yang unik agar data yang dikirim dapat sampai ke tujuan yang benar.

**Fungsi IP Address dalam Jaringan**

- **Identifikasi perangkat** — membedakan satu perangkat dengan perangkat lainnya dalam jaringan.
- **Pengalamatan lokasi** — menentukan di jaringan mana suatu perangkat berada.
- **Routing** — membantu router menentukan jalur terbaik untuk mengirimkan paket data.
- **Komunikasi data** — syarat mutlak agar dua perangkat dapat saling bertukar data menggunakan protokol TCP/IP.

**IPv4 dan Karakteristiknya**

IPv4 terdiri dari 32 bit yang dibagi menjadi 4 oktet (masing-masing 8 bit), ditulis dalam format desimal bertitik (*dotted decimal notation*), misalnya `192.168.1.1`. Setiap oktet bernilai 0–255, dengan total kombinasi sekitar 4,3 miliar alamat, dan dibagi ke dalam beberapa kelas:

| Kelas | Range Oktet Pertama | Default Subnet Mask | Keterangan |
|---|---|---|---|
| A | 1 – 126 | 255.0.0.0 (/8) | Jaringan berskala sangat besar |
| B | 128 – 191 | 255.255.0.0 (/16) | Jaringan berskala menengah |
| C | 192 – 223 | 255.255.255.0 (/24) | Jaringan berskala kecil |
| D | 224 – 239 | – | Digunakan untuk multicast |
| E | 240 – 255 | – | Dicadangkan untuk riset/eksperimen |

**Struktur IPv4**

Sebuah IPv4 Address terdiri dari 32 bit biner yang dikelompokkan menjadi 4 oktet, dipisahkan tanda titik:
```
Contoh :  192.168.1.10
Biner  :  11000000.10101000.00000001.00001010
```
Alamat IPv4 secara logis terbagi menjadi **Network ID** (identitas jaringan) dan **Host ID** (identitas perangkat), yang ditentukan oleh subnet mask atau prefix CIDR.

**Network Address, Host Address, dan Broadcast Address**

- **Network Address** — merepresentasikan suatu jaringan secara keseluruhan; seluruh bit host bernilai 0; tidak dapat digunakan sebagai alamat perangkat.
- **Host Address** — alamat unik yang diberikan kepada masing-masing perangkat dalam jaringan.
- **Broadcast Address** — seluruh bit host bernilai 1; digunakan untuk mengirim data ke seluruh perangkat dalam satu jaringan sekaligus.

> ⚠️ **Catatan:** Network Address dan Broadcast Address tidak dapat digunakan sebagai alamat host.

**Subnet Mask**

Subnet mask adalah angka 32 bit yang memisahkan bagian Network ID dan Host ID pada sebuah alamat IP. Bit bernilai 1 menandakan bagian network, bit bernilai 0 menandakan bagian host.

| Subnet Mask (Desimal) | Prefix (CIDR) | Jumlah Bit Network |
|---|---|---|
| 255.0.0.0 | /8 | 8 bit |
| 255.255.0.0 | /16 | 16 bit |
| 255.255.255.0 | /24 | 24 bit |
| 255.255.255.128 | /25 | 25 bit |
| 255.255.255.192 | /26 | 26 bit |

**CIDR (Classless Inter-Domain Routing)**

CIDR adalah metode penulisan subnet mask yang lebih ringkas dengan menuliskan jumlah bit network setelah tanda garis miring (`/`) di belakang alamat IP, misalnya `192.168.1.0/24`. Metode ini menggantikan sistem pembagian kelas (*classful*) yang kurang fleksibel, sehingga administrator dapat membagi alamat IP sesuai kebutuhan jumlah host, tanpa terpaku pada batasan kelas A, B, atau C.

**Private IP Address dan Public IP Address**

*Private IP Address* hanya digunakan dalam lingkup jaringan lokal (internal) dan tidak dapat diakses langsung dari internet. *Public IP Address* bersifat unik secara global dan dapat diakses langsung melalui internet.

| Kelas | Rentang Private IP Address | CIDR |
|---|---|---|
| A | 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| B | 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| C | 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

**IP Address Khusus yang Perlu Diketahui**

| Alamat | Keterangan |
|---|---|
| 127.0.0.1 | *Loopback address*, menguji perangkat itu sendiri (localhost) |
| 0.0.0.0 | Merepresentasikan alamat tidak diketahui / seluruh alamat (default route) |
| 255.255.255.255 | *Limited broadcast address*, mengirim data ke seluruh perangkat dalam segmen lokal |
| 169.254.x.x | APIPA, diberikan otomatis saat perangkat gagal memperoleh IP dari DHCP |

---

### 2. Pengenalan Topologi Jaringan

Topologi jaringan menggambarkan bagaimana perangkat-perangkat dalam suatu jaringan saling terhubung, baik secara fisik maupun logis, dan memengaruhi performa, biaya instalasi, kemudahan pemeliharaan, serta ketahanan jaringan terhadap gangguan.

**Physical Topology vs Logical Topology**

- **Physical Topology** — tata letak fisik perangkat dan kabel yang menghubungkan setiap node, sesuai kondisi nyata di lapangan.
- **Logical Topology** — cara data mengalir dalam jaringan, terlepas dari bentuk fisiknya.

**Jenis-Jenis Topologi**

| Topologi | Deskripsi | Kelebihan | Kekurangan |
|---|---|---|---|
| **Bus** | Seluruh perangkat terhubung ke satu kabel utama (*backbone*) secara linear | Instalasi sederhana, hemat kabel | Jika kabel utama putus, seluruh jaringan terganggu; rawan *collision* |
| **Star** | Seluruh perangkat terhubung ke satu titik pusat (switch/hub) | Mudah dikelola, kerusakan 1 perangkat tidak memengaruhi lainnya | Jika perangkat pusat rusak, seluruh jaringan terputus |
| **Ring** | Setiap perangkat terhubung ke 2 perangkat lain membentuk lingkaran | Aliran data teratur, performa stabil | Jika 1 node/segmen putus, jaringan terganggu (kecuali dual ring) |
| **Mesh** | Setiap perangkat terhubung langsung ke perangkat lain (*full/partial mesh*) | Sangat andal (*redundant*), banyak jalur alternatif | Biaya & kompleksitas kabel tinggi |
| **Tree** | Gabungan beberapa topologi star yang terhubung ke satu backbone (hierarkis) | Cocok untuk jaringan besar/bertingkat, mudah dikembangkan | Jika node backbone terganggu, seluruh cabang di bawahnya terdampak |

**Contoh Topologi**

Topologi Bus

<img width="984" height="499" alt="image" src="https://github.com/user-attachments/assets/3211beba-93bf-4e3b-b17e-7bbb7526c6de" />

Topologi Star

<img width="696" height="574" alt="image" src="https://github.com/user-attachments/assets/7ddd7b30-547a-48c1-93ef-f45704e169ac" />

Topologi Ring

<img width="659" height="453" alt="image" src="https://github.com/user-attachments/assets/25331359-cb14-4a48-882f-abe2071a28c6" />

Topologi Mesh

<img width="368" height="271" alt="image" src="https://github.com/user-attachments/assets/2a269396-469d-49d7-bd29-5927438b3f05" />

Topologi Tree

<img width="640" height="480" alt="image" src="https://github.com/user-attachments/assets/3e4639af-1f98-4525-a342-e85e397293a8" />


**Perangkat Dasar dalam Topologi Jaringan**

| Perangkat | Fungsi Utama |
|---|---|
| Hub | Menghubungkan beberapa perangkat, meneruskan data ke seluruh port tanpa penyaringan |
| Switch | Meneruskan data hanya ke port tujuan berdasarkan MAC Address |
| Router | Menghubungkan dua jaringan berbeda dan menentukan jalur routing antar jaringan |
| Access Point | Menghubungkan perangkat nirkabel (*wireless*) ke jaringan kabel (*wired*) |
| Kabel (UTP/Fiber Optic) | Media transmisi data secara fisik antar perangkat |

**Pemilihan Topologi Berdasarkan Kebutuhan Jaringan**
Pertimbangan pemilihan topologi meliputi: skala jaringan, anggaran (*budget*), tingkat keandalan yang dibutuhkan, kemudahan pemeliharaan, dan lingkungan fisik lokasi jaringan dibangun.

> 💡 **Catatan:** Topologi star umum digunakan untuk jaringan LAN skala kecil-menengah karena kemudahan instalasi dan manajemennya, sedangkan topologi tree/hierarki umum digunakan pada jaringan skala kampus atau perkantoran bertingkat.

---

### 3. Dasar-Dasar Subnetting

**Pengertian Subnetting**

Subnetting adalah proses membagi satu blok alamat IP (network) menjadi beberapa jaringan yang lebih kecil (*subnet*), dengan meminjam sejumlah bit dari bagian Host ID untuk dijadikan bit tambahan pada Network ID.

**Tujuan dan Manfaat Subnetting**

- Efisiensi penggunaan alamat IP.
- Mempermudah manajemen jaringan (segmentasi).
- Meningkatkan keamanan (memisahkan segmen jaringan antar-divisi).
- Mengurangi *traffic broadcast* (broadcast domain lebih kecil).
- Mendukung struktur organisasi jaringan (per gedung/lantai/divisi).

**Hubungan CIDR dengan Subnetting**

Notasi CIDR (`/24`, `/25`, `/26`, dst.) menentukan berapa banyak bit yang dialokasikan untuk Network ID, termasuk bit yang dipinjam dari Host ID. Semakin besar prefix, semakin banyak subnet yang dihasilkan namun semakin sedikit host per subnet.

**Network Bit dan Host Bit**

Dalam 32 bit IPv4:
- **Network Bit** — mengidentifikasi jaringan, ditentukan subnet mask/prefix CIDR.
- **Host Bit** — sisanya, mengidentifikasi masing-masing perangkat dalam jaringan.

**Jumlah Subnet dan Jumlah Host**

```
Jumlah Subnet          = 2^n       (n = jumlah bit yang dipinjam dari host bit)
Jumlah Host per Subnet = 2^h - 2   (h = jumlah bit host yang tersisa)
```
> ⚠️ Pengurangan 2 karena 1 alamat dipakai sebagai Network Address dan 1 alamat sebagai Broadcast Address, sehingga keduanya tidak dapat dipakai sebagai alamat host.

**Subnet Mask dan Block Size**

*Block size* (increment) adalah selisih antar network address yang berurutan:
```
Block Size = 256 - (nilai oktet subnet mask yang terpengaruh)
```
Contoh: subnet mask oktet terakhir 192 (`/26`) → block size = 256 − 192 = 64, artinya setiap subnet berjarak 64 alamat IP (0, 64, 128, 192, ...).

**Network Address, Broadcast Address, dan Host Range**

- **Network Address** — alamat pertama pada subnet (seluruh bit host = 0).
- **Broadcast Address** — alamat terakhir pada subnet (seluruh bit host = 1).
- **Host Range** — rentang alamat yang dapat digunakan, yaitu `(Network Address + 1)` sampai `(Broadcast Address − 1)`.

---

### 4. Perhitungan Subnetting IPv4
 
**Langkah-Langkah Perhitungan Subnetting**
1. Tentukan alamat IP dan prefix CIDR yang diketahui (misal `192.168.10.150/27`).
2. Hitung **Total IP** dalam satu blok subnet menggunakan CIDR.
3. Hitung **Usable Host**, yaitu jumlah host yang benar-benar dapat digunakan.
4. Tentukan **Network Address (NA)** — blok subnet tempat IP tersebut berada.
5. Tentukan **Broadcast Address (BC)** dari blok subnet tersebut.
6. Tentukan **Host Range** (host minimum dan host maksimum).
7. Tentukan **Subnet Mask (SM)** dari blok subnet tersebut.
   
**Ringkasan Rumus Subnetting — Cheat Sheet**
 
| Yang Dicari | Rumus |
|---|---|
| Total IP | `2^(32 − CIDR)` |
| Usable Host | `Total IP − 2` |
| Network Address (NA) | `(Nilai Oktet IP ÷ Total IP) × Total IP` *(hasil bagi dibulatkan ke bawah)* |
| Broadcast Address (BC) | `NA + Total IP − 1` |
| Host Minimum | `NA + 1` |
| Host Maximum | `BC − 1` |
| Subnet Mask (SM) | `255.255.255.256 − Total IP` |
 
**Contoh Perhitungan**
 
Diketahui alamat IP: `192.168.10.150/27`. Tentukan Total IP, Usable Host, Network Address, Broadcast Address, Host Range, dan Subnet Mask.
 
**1. Total IP**
```
Rumus  : Total IP = 2^(32 - CIDR)
Hitung : = 2^(32 - 27) = 2^5
Hasil  : Total IP = 32 IP
```
 
**2. Usable Host**
```
Rumus  : Usable Host = Total IP - 2
Hitung : = 32 - 2
Hasil  : Usable Host = 30 Host
```
 
**3. Network Address (NA)**
```
Rumus  : NA = (Nilai Oktet 4 IP ÷ Total IP) × Total IP
Hitung : = 150 ÷ 32 = 4,xxx  →  4 × 32
Hasil  : NA = 192.168.10.128
```
 
**4. Broadcast Address (BC)**
```
Rumus  : BC = NA + Total IP - 1
Hitung : = 128 + 32 - 1
Hasil  : BC = 192.168.10.159
```
 
**5. Host Range**
```
Host Minimum = NA + 1  = 128 + 1 = 129
Host Maximum = BC - 1  = 159 - 1 = 158
Hasil : Host Range = 192.168.10.129 - 192.168.10.158
```
 
**6. Subnet Mask (SM)**
```
Rumus  : SM = 255.255.255.256 - Total IP
Hitung : = 255.255.255.256 - 32
Hasil  : SM = 255.255.255.224
```
 
> 💡 **Kesimpulan:** IP `192.168.10.150/27` berada pada subnet `192.168.10.128/27`, dengan Network Address `192.168.10.128`, Broadcast Address `192.168.10.159`, Host Range `192.168.10.129 – 192.168.10.158`, dan Subnet Mask `255.255.255.224`.
 
**Contoh 2: Subnetting berdasarkan kebutuhan jumlah host**
 
Sebuah kantor memiliki jaringan `192.168.10.0/24` dan membutuhkan subnet yang mampu menampung minimal 25 host per subnet. Tentukan CIDR yang sesuai, lalu hitung subnet pertamanya.
 
1. Cari CIDR dengan Usable Host ≥ 25. Coba `/27`: Total IP = 2^(32−27) = 32 → Usable Host = 32 − 2 = **30 Host** (mencukupi). Coba `/28`: Total IP = 2^(32−28) = 16 → Usable Host = 16 − 2 = 14 Host (tidak mencukupi).
2. Maka CIDR yang dipakai adalah **/27**.
3. Hitung subnet pertama untuk IP `192.168.10.0/27`:
```
Total IP : 2^(32-27) = 32
NA       : (0 ÷ 32) × 32 = 0        → 192.168.10.0
BC       : 0 + 32 - 1  = 31         → 192.168.10.31
Host Min : 0 + 1  = 1               → 192.168.10.1
Host Max : 31 - 1 = 30              → 192.168.10.30
SM       : 255.255.255.256 - 32 = 255.255.255.224
```
 
4. Subnet berikutnya tinggal menambahkan kelipatan Total IP (32) pada oktet terakhir: `192.168.10.32/27`, `192.168.10.64/27`, `192.168.10.96/27`, dan seterusnya.
   
**Tabel Referensi Cepat CIDR /25 – /30**
 
| Prefix CIDR | Total IP | Subnet Mask | Usable Host |
|---|---|---|---|
| /25 | 128 | 255.255.255.128 | 126 |
| /26 | 64 | 255.255.255.192 | 62 |
| /27 | 32 | 255.255.255.224 | 30 |
| /28 | 16 | 255.255.255.240 | 14 |
| /29 | 8 | 255.255.255.248 | 6 |
| /30 | 4 | 255.255.255.252 | 2 |
 
> 💡 **Catatan:** Kerjakan perhitungan subnetting secara manual dengan rumus di atas (tanpa kalkulator subnetting online) terlebih dahulu, agar benar-benar memahami konsep pembagian blok Network Address, Broadcast Address, dan Host Range sebelum menggunakan alat bantu otomatis.


### 5. Tugas & Evaluasi Praktikum
1. Tentukan Total IP, Usable Host, Network Address, Broadcast Address, Host Range, dan Subnet Mask dari `192.168.5.70/26`!
2. Tentukan Total IP, Usable Host, Network Address, Broadcast Address, Host Range, dan Subnet Mask dari `172.16.30.100/27`!
3. Tentukan CIDR yang paling sesuai (paling efisien) untuk sebuah ruang laboratorium yang membutuhkan jaringan untuk 20 perangkat, lalu buktikan dengan perhitungan Usable Host!
4. Jelaskan perbedaan antara Network Address, Host Address, dan Broadcast Address, disertai contoh alamat IP!
5. Jelaskan perbedaan Private IP Address dan Public IP Address, serta sebutkan salah satu rentang Private IP Address!
6. Jelaskan perbedaan topologi Star dan topologi Bus, lengkap dengan satu kelebihan dan satu kekurangan masing-masing!


## 📝 Catatan
- Link pengumpulan: [link gform]
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer

## 📚 Referensi
- Modul Praktikum Jaringan Komputer — Pertemuan 1: Pengenalan IP Address, Topologi Jaringan, dan Dasar-Dasar Subnetting
