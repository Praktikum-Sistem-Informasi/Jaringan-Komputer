# Pertemuan 4: Static Routing & Dynamic Routing (RIP)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar routing serta perbedaan mendasar antara static routing dan dynamic routing pada jaringan komputer.
- Praktikan mampu melakukan konfigurasi dasar dengan mengalamatan IP pada interface perangkat Router Cisco.
- Praktikan mampu mengonfigurasi static route secara manual untuk menghubungkan antar network yang berbeda pada topologi multi router.
- Praktikan mampu memahami karakteristik dan mekanisme kerja protokol routing dinamis RIP (Routing Information Protocol).
- Praktikan mampu mengonfigurasi dynamic routing menggunakan RIP untuk melakukan pertukaran informasi routing secara otomatis antar router.
- Praktikan mampu melakukan verifikasi dan troubleshooting tabel routing menggunakan perintah show ip route pada CLI Cisco.


## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: ?.
- `docs/` — berisi modul ini beserta materi pendukung lain(?).

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

### 1. Routing
Routing adalah teknologi pada router untuk menentukan jalur terbaik pengiriman data antar jaringan, yang sangat mempengaruhi konektivitas dan kecepatan transmisi data.

Terdapat dua jenis routing: static routing, yaitu konfigurasi jalur transmisi data secara manual oleh administrator, cocok untuk jaringan kecil karena stabil dan aman namun kurang fleksibel untuk jaringan skala menengah-besar; dan dynamic routing, yaitu metode di mana router saling bertukar informasi jalur secara otomatis menggunakan protokol tertentu berdasarkan metrik seperti kecepatan atau jarak, sehingga lebih fleksibel dan cocok untuk jaringan skala menengah-besar.

Sedangkan dynamic routing adalah metode
pengaturan jalur jaringan di mana perangkat jaringan, seperti router, menggunakan
protokol khusus untuk berkomunikasi satu sama lain dan secara otomatis memperbarui
tabel routing mereka. Protokol ini memungkinkan perangkat-perangkat tersebut berbagi
informasi tentang jaringan dan memutuskan jalur terbaik untuk mengirimkan data
berdasarkan metrik tertentu, seperti kecepatan atau jarak. Dynamic routing memiliki
keunggulan pada fleksibilitas, dinamika update penentuan jalur transmisi dan kemudahan
implementasi pada jaringan berskala menengah hingga besar.

### 2. Static Routing

Static routing merupakan sebuah fitur untuk menetapkan jalur transmisi data dalam jaringan komputer secara manual dengan mendaftarkan setiap jaringan yang ada pada 
daftar routing dalam sebuah perangkat router. Jadi dapat disimpulkan bahwa konfigurasi routing hanya dikonfigurasikan pada perangkat jaringan yang memiliki kemampuan untuk
menjalankan fungsi routing seperti router dan switch layer 3. Static routing perlu dilakukan ketika jaringan yang ditujukan saat berkomunikasi berjarak lebih dari 1 hop dari jaringan yang menginisiasi transmisi data.

### 3. Dynamic Routing (RIP)

Dynamic routing memungkinkan router saling bertukar informasi jalur secara otomatis untuk menentukan rute terbaik. RIP (Routing Information Protocol) adalah salah satu protokol dynamic routing paling sederhana, termasuk kategori interior routing protocol untuk jaringan lokal.

RIP bekerja dengan menukar informasi antar-router tetangga dan menentukan jalur terbaik berdasarkan hop count (jumlah router yang dilewati) jadi semakin sedikit hop, semakin baik. Update tabel routing dikirim secara periodik tiap 30 detik. Kelemahan utamanya adalah batas maksimum 15 hop, jadi kalau tujuannya lebih jauh dari itu, RIP tidak bisa menjangkau.

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer

