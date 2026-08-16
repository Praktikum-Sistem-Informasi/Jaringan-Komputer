# Pertemuan 6: DHCP (Dynamic Host Configuration Protocol)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar DHCP dan proses DORA (Discover, Offer, Request, Acknowledge) dalam pemberian alamat IP secara otomatis.
- Praktikan mampu mengonfigurasi DHCP server bawaan (IOS) pada perangkat Router.
- Praktikan mampu mengonfigurasi layanan DHCP pada perangkat Server di Cisco Packet Tracer.
- Praktikan mampu mengonfigurasi topologi yang menggabungkan DHCP router dan DHCP server secara bersamaan tanpa terjadi konflik scope.
- Praktikan mampu menjelaskan alasan penggunaan DHCP server (bukan hanya router) untuk kebutuhan jaringan berskala besar.
- Praktikan mampu melakukan verifikasi konfigurasi DHCP menggunakan perintah `show ip dhcp binding`, `show ip dhcp pool`, dan pengecekan IP pada sisi client.

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/`: berisi skenario tugas rancangan topologi dengan DHCP router dan DHCP server berjalan pada segmen berbeda.
- `docs/`: berisi modul ini beserta materi pendukung lain (cheatsheet CLI, referensi DHCP).

## 🚀 Cara Menjalankan
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
# 1. Buka file topologi pada Cisco Packet Tracer, pastikan untuk mematikan internet
# 2. Klik perangkat Router, buka tab CLI, lalu masukkan perintah konfigurasi, contoh:
Router> enable
Router# configure terminal
Router(config)#
```

## 📖 Materi Praktikum

### 1. Konsep Dasar DHCP
DHCP (Dynamic Host Configuration Protocol) adalah protokol yang digunakan untuk memberikan konfigurasi IP address, subnet mask, default gateway, dan DNS secara otomatis kepada client, tanpa perlu konfigurasi manual satu per satu. Proses pemberian alamat ini dikenal sebagai **DORA**:

- **Discover**. Client mengirim broadcast untuk mencari DHCP server yang aktif di jaringan.
- **Offer**. DHCP server merespons dengan menawarkan satu alamat IP yang tersedia.
- **Request**. Client meminta secara resmi alamat IP yang ditawarkan tersebut.
- **Acknowledge**. Server mengonfirmasi, dan client resmi menggunakan alamat IP tersebut untuk periode waktu tertentu (lease time).

Sesuai arahan Praz, DHCP tetap dipakai pada praktikum ini karena mekanismenya merepresentasikan kondisi jaringan nyata, di mana alokasi IP jarang dilakukan secara statis satu per satu. Pada pertemuan ini, DHCP dikonfigurasi menggunakan **kombinasi antara Router dan Server** dalam satu topologi.

### 2. Konfigurasi DHCP pada Router

Router Cisco memiliki fitur DHCP server bawaan (IOS DHCP Server) yang bisa langsung diaktifkan lewat CLI, tanpa perlu perangkat tambahan. Cocok dipakai untuk segmen jaringan kecil/lokal.

**Cheatsheet CLI Konfigurasi DHCP Router:**

| Fungsi | Perintah CLI | Penjelasan |
|---|---|---|
| Mengecualikan alamat | `Router(config)# ip dhcp excluded-address [ip_awal] [ip_akhir]` | Mencadangkan alamat tertentu (biasanya gateway) agar tidak dibagikan ke client. |
| Membuat pool DHCP | `Router(config)# ip dhcp pool [nama_pool]` | Membuat sekaligus masuk ke mode konfigurasi pool DHCP. |
| Menentukan network | `Router(dhcp-config)# network [network_id] [subnet_mask]` | Menentukan rentang alamat yang akan dibagikan ke client. |
| Menentukan gateway | `Router(dhcp-config)# default-router [ip_gateway]` | Menentukan default gateway yang diterima client. |
| Menentukan DNS | `Router(dhcp-config)# dns-server [ip_dns]` | Menentukan alamat DNS server yang diterima client. |
| Mengatur lease time | `Router(dhcp-config)# lease [hari] [jam] [menit]` | Mengatur durasi sewa alamat IP (default 24 jam). |
| Melihat binding | `Router# show ip dhcp binding` | Menampilkan daftar IP yang sudah disewakan ke client beserta MAC address-nya. |
| Melihat pool | `Router# show ip dhcp pool` | Menampilkan status dan statistik pool DHCP yang aktif. |

**Langkah kerja:**
```
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
Router(config)# ip dhcp pool LAN_ROUTER
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit
```

**Uji Verifikasi:** Ubah pengaturan IP pada PC client di segmen tersebut menjadi **DHCP**, lalu jalankan `ipconfig` (di Command Prompt PC). PC harus menerima IP sesuai rentang `network` yang dikonfigurasi. Jalankan juga `show ip dhcp binding` di router untuk memastikan IP client tercatat.

### 3. Konfigurasi DHCP pada Server

Untuk segmen jaringan yang lebih besar, DHCP dijalankan dari perangkat **Server** (bukan router) menggunakan fitur *Services* di Packet Tracer.

**Langkah kerja:**
1. Klik perangkat **Server**, buka tab **Desktop**, pilih **IP Configuration**, lalu set IP address server secara **Static** (server tidak boleh dapat IP dari DHCP-nya sendiri).
2. Masih di perangkat Server, buka tab **Config**, pilih menu **DHCP** pada bagian Services.
3. Isi parameter layanan:
   - **Service**: On
   - **Default Gateway**: sesuai gateway segmen tersebut
   - **DNS Server**: sesuai kebutuhan
   - **Start IP Address**: alamat awal rentang yang dibagikan
   - **Subnet Mask**: sesuai segmen
   - **Maximum Number of Users**: sesuai kebutuhan jumlah client
4. Klik **Save**, lalu pastikan status pool menjadi aktif.

**Uji Verifikasi:** Sambungkan PC ke segmen server tersebut, set IP Configuration PC ke **DHCP**, lalu cek apakah PC menerima IP sesuai rentang yang dikonfigurasi di server.

### 4. Kombinasi DHCP Router dan Server dalam Satu Topologi

Karena DHCP router dan DHCP server berjalan bersamaan dalam satu topologi, **konfigurasi pool/scope di router tidak boleh sama dengan konfigurasi di server**, baik dari sisi network address maupun rentang IP yang dibagikan. Jika keduanya menggunakan rentang yang sama atau tumpang tindih, akan terjadi **konflik alamat (IP conflict)**, di mana dua perangkat berbeda berpotensi mendapatkan IP yang identik dari dua sumber DHCP yang berbeda.

**Contoh pembagian scope yang benar (tidak bentrok):**

| Sumber DHCP | Segmen | Network | Rentang yang dibagikan |
|---|---|---|---|
| Router | LAN lokal (kecil) | 192.168.10.0/24 | 192.168.10.11 – 192.168.10.254 |
| Server | LAN besar | 192.168.20.0/24 | 192.168.20.11 – 192.168.20.254 |

> ⚠️ **Peringatan Konfigurasi:** Jangan pernah menyamakan `network` pada pool router dengan rentang start/end pada server untuk segmen yang sama. Selalu pastikan setiap sumber DHCP memiliki rentang alamat yang eksklusif dan tidak tumpang tindih satu sama lain sebelum melakukan uji koneksi.

**Uji Verifikasi Gabungan:** Sambungkan PC pada masing-masing segmen (segmen router dan segmen server), set keduanya ke DHCP, lalu pastikan setiap PC menerima IP dari sumber DHCP yang seharusnya (bukan tertukar), dan tidak ada duplikasi IP antar segmen saat dicek dengan `show ip dhcp binding` di router serta daftar client di server.

### 5. Alasan Penggunaan DHCP Server

Meskipun router juga mampu menjalankan fungsi DHCP, penggunaan DHCP server tersendiri lebih relevan untuk kebutuhan **jaringan berskala besar**. Beberapa alasannya:

- Server DHCP dirancang untuk menangani volume permintaan IP yang jauh lebih banyak dan lebih stabil dibanding fitur DHCP bawaan router.
- Manajemen lebih terpusat, sehingga administrasi, monitoring, dan pencatatan (logging) alokasi IP lebih mudah dilakukan dari satu titik.
- Lebih mudah diskalakan ketika jaringan bertambah besar, tanpa membebani performa router yang idealnya fokus pada fungsi routing.

Karena itu, kombinasi router (untuk segmen kecil/lokal) dan server (untuk kebutuhan jaringan besar) menjadi pendekatan yang lebih realistis dibanding hanya mengandalkan salah satunya.

## 📝 Catatan
- 

## 📚 Referensi
- Cisco. *IP Addressing: DHCP Configuration Guide, Configuring the Cisco IOS DHCP Server*. [cisco.com](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/15-mt/dhcp-15-mt-book/config-dhcp-server.html)
- Droms, R. *RFC 2131, Dynamic Host Configuration Protocol*. Internet Engineering Task Force (IETF). [datatracker.ietf.org/doc/html/rfc2131](https://datatracker.ietf.org/doc/html/rfc2131)
