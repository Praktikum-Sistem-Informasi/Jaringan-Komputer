# Soal Praktikum Singkat, Pertemuan 4: Static Routing dan Dynamic Routing RIP

## 📋 Deskripsi Tugas
Praktikan diminta membuat dua topologi dalam satu file .pkt yang sama, saling terpisah (tidak terhubung satu sama lain): Topologi A menggunakan static routing, dan Topologi B menggunakan dynamic routing RIP. Setiap router pada kedua topologi memiliki switch dan PC sendiri, dan koneksi antar-router dilakukan langsung melalui port Ethernet (tanpa modul serial).

## 🧰 Alat & Bahan
Untuk membuat kedua topologi dalam satu file, total perangkat yang dibutuhkan:

- Aplikasi Cisco Packet Tracer
- 6 unit Router Cisco (3 untuk Topologi A, 3 untuk Topologi B)
- 6 unit Switch (1 per router)
- 6 unit PC (1 per router)
- Kabel Straight (Router ke Switch, Switch ke PC)
- Kabel Crossover (Router ke Router)

## 🗺️ Topologi Jaringan (berlaku untuk Topologi A dan B)

| Perangkat | Keterangan |
|---|---|
| R1 | Punya LAN sendiri (Switch1-PC1), terhubung ke R2, dan R3 |
| R2 | Punya LAN sendiri (Switch2-PC2), terhubung ke R1 dan R3 |
| R3 | Punya LAN sendiri (Switch3-PC3), terhubung ke R2 dan R1 |

Gunakan pengalamatan berikut sebagai acuan (mau pake intervlan juga boleh):

## Tabel Interface Router

| Router | Interface | IP Address        | Terhubung ke           |
|--------|-----------|-------------------|------------------------|
| R1     | Fa0/0     | 192.168.1.1/24    | Switch1 (LAN R1)       |
| R1     | Fa0/1     | 1.1.1.1/30     | R2                     |
| R1     | Fa1/0     | 2.2.2.1/30     | R3                     |
| R2     | Fa0/0     | 192.168.2.1/24    | Switch2 (LAN R2)       |
| R2     | Fa0/1     | 1.1.1.2/30     | R1                     |
| R2     | Fa1/0     | 3.3.3.1/30     | R3                     |
| R3     | Fa0/0     | 192.168.3.1/24    | Switch3 (LAN R3)       |
| R3     | Fa0/1     | 3.3.3.2/30     | R2                     |
| R3     | Fa1/0     | 2.2.2.2/30     | R1                     |

## Tabel Host (PC)

| PC  | IP Address     | Subnet Mask      | Gateway        |
|-----|----------------|------------------|----------------|
| PC1 | 192.168.1.2    | 255.255.255.0    | 192.168.1.1    |
| PC2 | 192.168.2.2    | 255.255.255.0    | 192.168.2.1    |
| PC3 | 192.168.3.2    | 255.255.255.0    | 192.168.3.1    |

## 📝 Instruksi Pengerjaan

### Langkah 1. Susun Kerangka Topologi 
Buka file `.pkt` baru. Tempatkan dua kelompok perangkat pada canvas yang sama namun terpisah jauh dan tidak saling terhubung. Beri label teks (tool Text) "TOPOLOGI A - STATIC" dan "TOPOLOGI B - RIP" agar mudah dibedakan.

### Langkah 2. Bangun Topologi A - Static Routing 
Susun R1, R2, R3 membentuk ring (tiap router terhubung ke dua router lain) dengan kabel Crossover, masing-masing dengan 1 switch dan 1 PC sendiri via kabel Straight-Through. Berikan hostname, konfigurasikan IP address pada seluruh interface sesuai tabel (termasuk `no shutdown`). Tambahkan `ip route` pada tiap router menuju kedua LAN tetangga melalui link langsung terdekat (misalnya di R1: rute ke LAN R2 lewat link R1-R2, rute ke LAN R3 lewat link R1-R3). Verifikasi dengan `show ip route`.

### Langkah 3. Bangun Topologi B - Dynamic Routing RIP 
Ulangi skema fisik dan pengalamatan yang sama seperti Topologi A, tapi kali ini jangan tambahkan `ip route`. Sebagai gantinya, aktifkan RIP pada R1, R2, dan R3 dengan mendaftarkan seluruh network yang terhubung langsung menggunakan perintah `network`. Verifikasi dengan `show ip route` dan `show ip protocols`.

### Langkah 4. Uji Konektivitas Awal 
Pada kedua topologi, lakukan `ping` dari PC1 ke PC2 dan PC3. Pastikan semua berhasil sebelum lanjut ke langkah berikutnya.

### Langkah 5. Uji Ketahanan Ring saat Salah Satu Link Terputus 
Pada kedua topologi, putuskan link R1–R2 (klik kabel lalu Delete, atau shutdown pada interface terkait di kedua ujung). Ulangi `ping` dari PC1 ke PC2 (yang jalur langsungnya baru saja terputus, tapi masih ada jalur alternatif lewat R3). Amati:

- Apakah PC1 masih bisa mencapai PC2 lewat jalur R1-R3-R2?
- Apakah tabel routing diperbarui otomatis untuk memakai jalur alternatif tersebut?

### Langkah 6. Rapikan dan Kumpulkan 
Simpan konfigurasi seluruh router pada kedua topologi (`write atau` atau `copy run start` ). 

Ambil screenshot `show ip route` dari Topologi A dan B, serta hasil `ping` PC1 ke PC2 sebelum dan sesudah link R1 ke R2 diputus.

## 📊 Tabel Hasil Uji Coba

| No | Pengujian                                                      | Topologi A (Static) | Topologi B (RIP) |
|----|----------------------------------------------------------------|---------------------|------------------|
| 1  | Kode route yang muncul pada `show ip route` (S/R)                    |                     |                  |
| 2  | Ping PC1 ke PC2 sebelum link R1-R2 diputus                     |                     |                  |
| 3  | Ping PC1 ke PC2 setelah link R1-R2 diputus                     |                     |                  |
| 4  | Apakah jalur alternatif (via R3) otomatis dipakai setelah link diputus? |                     |                  |

## ❓ Pertanyaan Analisis
1. Berdasarkan hasil Langkah 5, mengapa Topologi A (static) gagal memanfaatkan jalur alternatif lewat R3 padahal secara fisik jalur itu masih ada, sedangkan Topologi B (RIP) bisa?
2. Apa yang perlu ditambahkan pada konfigurasi static routing di Topologi A agar PC1 tetap bisa mencapai PC2 lewat R3 saat link R1–R2 terputus?
   
## 📤 Ketentuan Pengumpulan
- File topologi .pkt (berisi kedua topologi dalam satu file) dikumpulkan pada folder src/.
- Tabel hasil uji coba dan jawaban pertanyaan analisis dikumpulkan langsung pada file ini.
- Sertakan screenshot show ip route dari kedua topologi serta hasil ping sebelum dan sesudah link diputus.
- Batas waktu pengumpulan:?
