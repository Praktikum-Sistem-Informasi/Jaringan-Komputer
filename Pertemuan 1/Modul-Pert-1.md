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

<img width="820" height="361" alt="image" src="https://github.com/user-attachments/assets/8f196d13-1dd5-494c-9cef-5f323d77ef86" />

Topologi Star

<img width="540" height="480" alt="image" src="https://github.com/user-attachments/assets/787fcc3d-41fc-4bbb-bfef-824f2a42187b" />

Topologi Ring

<img width="659" height="453" alt="image" src="https://github.com/user-attachments/assets/25331359-cb14-4a48-882f-abe2071a28c6" />

Topologi Mesh

<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/88c400d3-1d40-4083-aa2a-2641b1818050" />

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

1. Tentukan alamat IP dan prefix CIDR awal yang akan dibagi (misal `192.168.1.0/24`).
2. Tentukan kebutuhan: berdasarkan jumlah subnet atau jumlah host per subnet.
3. Hitung jumlah bit yang perlu dipinjam dari host bit (`2^n`).
4. Tentukan subnet mask baru dan notasi CIDR baru.
5. Hitung block size untuk menentukan batas-batas setiap subnet.
6. Tentukan Network Address, Broadcast Address, dan Host Range setiap subnet.

**Rumus Cepat**

```
Jumlah Total IP     = 2^h        (h = jumlah bit host, termasuk network & broadcast address)
Jumlah Host Usable  = 2^h - 2
```

**Tabel Referensi Cepat CIDR /25 – /30**

| Prefix CIDR | Subnet Mask (Oktet Terpengaruh) | Block Size | Jumlah Host Usable |
|---|---|---|---|
| /25 | 128 | 128 | 126 |
| /26 | 192 | 64 | 62 |
| /27 | 224 | 32 | 30 |
| /28 | 240 | 16 | 14 |
| /29 | 248 | 8 | 6 |
| /30 | 252 | 4 | 2 |

**Contoh 1: Subnetting berdasarkan CIDR /26**

Diketahui alamat jaringan `192.168.1.0/26`. Tentukan subnet mask, jumlah host per subnet, network address, broadcast address, dan host range.
- Prefix `/26` → 26 bit network, 6 bit host (32 − 26 = 6).
- Subnet Mask: `255.255.255.192`
- Block Size = 256 − 192 = 64
- Jumlah Host Usable = 2⁶ − 2 = **62 host per subnet**

| Subnet ke- | Network Address | Host Range | Broadcast Address |
|---|---|---|---|
| 1 | 192.168.1.0 | 192.168.1.1 – 192.168.1.62 | 192.168.1.63 |
| 2 | 192.168.1.64 | 192.168.1.65 – 192.168.1.126 | 192.168.1.127 |
| 3 | 192.168.1.128 | 192.168.1.129 – 192.168.1.190 | 192.168.1.191 |
| 4 | 192.168.1.192 | 192.168.1.193 – 192.168.1.254 | 192.168.1.255 |

**Contoh 2: Subnetting berdasarkan kebutuhan jumlah host**

Sebuah kantor memiliki jaringan `192.168.10.0/24` dan membutuhkan subnet yang mampu menampung minimal 25 host per subnet. Tentukan CIDR yang sesuai.
1. Cari nilai `h` (bit host) terkecil sehingga `2^h - 2 ≥ 25`. Untuk h = 5: 2⁵ − 2 = 30 (mencukupi). Untuk h = 4: 2⁴ − 2 = 14 (tidak mencukupi).
2. Maka digunakan h = 5 bit host → bit network = 32 − 5 = 27, atau CIDR **/27**.
3. Subnet Mask untuk `/27` = `255.255.255.224`, block size = 256 − 224 = 32.
4. Subnet pertama: Network Address `192.168.10.0`, Host Range `192.168.10.1 – 192.168.10.30`, Broadcast Address `192.168.10.31`.
5. Subnet kedua: Network Address `192.168.10.32`, Host Range `192.168.10.33 – 192.168.10.62`, Broadcast Address `192.168.10.63`, dan seterusnya.

> 💡 **Catatan:** Kerjakan perhitungan subnetting secara manual (tanpa kalkulator subnetting online) terlebih dahulu, agar benar-benar memahami konsep pembagian bit network dan host sebelum menggunakan alat bantu otomatis.

### 5. Tugas & Evaluasi Praktikum
1. Jelaskan perbedaan antara Network Address, Broadcast Address, dan Host Address, disertai contoh alamat IP!
2. Gambarkan dan jelaskan perbedaan topologi Star dan topologi Mesh, lengkap dengan kelebihan dan kekurangannya!
3. Diberikan alamat jaringan `172.16.0.0/23`, tentukan subnet mask, block size, jumlah host usable, network address, broadcast address, dan host range untuk 2 subnet pertama jika jaringan tersebut disubnetting menjadi `/25`!
4. Sebuah perusahaan membutuhkan 6 subnet dengan alamat awal `10.10.10.0/24`. Tentukan CIDR yang sesuai dan tabel pembagian subnet-nya!
5. Jelaskan mengapa Private IP Address tidak dapat diakses langsung dari internet, dan sebutkan teknologi yang memungkinkan perangkat dengan Private IP Address tetap dapat mengakses internet!

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer — Pertemuan 1: Pengenalan IP Address, Topologi Jaringan, dan Dasar-Dasar Subnetting
