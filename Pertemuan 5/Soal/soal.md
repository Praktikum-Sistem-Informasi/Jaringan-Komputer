# Soal Praktikum Singkat, Pertemuan 5: Dynamic Routing EIGRP dan OSPF

## 📋 Deskripsi Tugas
Praktikan diminta membuat dua topologi ring dalam satu file .pkt, saling terpisah: Topologi A menggunakan EIGRP, dan Topologi B menggunakan OSPF. Skema fisik dan pengalamatan IP kedua topologi identik, sehingga praktikan bisa membandingkan langsung bagaimana kedua protokol memilih jalur terbaik dan bereaksi saat salah satu link pada ring terputus.

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
Buka file `.pkt` baru. Tempatkan dua kelompok perangkat pada canvas yang sama namun terpisah dan tidak saling terhubung. Beri label teks "TOPOLOGI A - EIGRP" dan "TOPOLOGI B - OSPF" agar mudah dibedakan.

### Langkah 2. Bangun Topologi A - EIGRP
Susun R1, R2, R3 membentuk ring dengan kabel Crossover, masing-masing dengan 1 switch dan 1 PC via kabel Straight. Berikan hostname dan konfigurasikan IP address pada seluruh interface sesuai tabel (termasuk no shutdown). Aktifkan EIGRP dengan Autonomous System yang sama di ketiga router, misalnya:
```
router eigrp 1
network 192.168.1.0 0.0.0.255
network 1.1.1.0 0.0.0.3
network 2.2.2.0 0.0.0.3
```
(sesuaikan network statement dengan interface masing-masing router). Verifikasi dengan `do show ip eigrp neighbors` dan `do show ip route eigrp`.

### Langkah 3. Bangun Topologi B - OSPF (20 menit)
Ulangi skema fisik dan pengalamatan yang sama seperti Topologi A. Aktifkan OSPF di ketiga router dengan process-id yang sama, misalnya:
```
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 1.1.1.0 0.0.0.3 area 0
network 2.2.2.0 0.0.0.3 area 0
```
(sesuaikan network statement dengan interface masing-masing router, seluruh interface masuk area 0). Verifikasi dengan show ip ospf neighbor dan show ip route ospf.

### Langkah 4. Uji Konektivitas Awal 
Pada kedua topologi, lakukan ping dari PC1 ke PC2 dan PC3. Pastikan semua berhasil sebelum lanjut.

### Langkah 5. Bandingkan Metric Jalur 
Pada kedua topologi, jalankan `do show ip route` dan catat metric yang tercantum di belakang setiap route (format `[AD/metric]`). Bandingkan cara EIGRP menghitung metric (komposit bandwidth & delay) dengan OSPF (cost berdasarkan bandwidth).

### Langkah 6. Uji Ketahanan Ring saat Salah Satu Link Terputus 
Pada kedua topologi, putuskan link R1–R2 (Delete kabel atau shutdown interface di kedua ujung). Ulangi ping dari PC1 ke PC2 (yang jalur langsungnya terputus, tapi masih ada jalur alternatif lewat R3). Amati:

Apakah PC1 masih bisa mencapai PC2 lewat jalur R1-R3-R2?
Berapa kira-kira waktu yang dibutuhkan sampai ping kembali normal (perhatikan jeda saat ping pertama kali gagal lalu berhasil lagi) pada EIGRP dibanding OSPF?

### Langkah 7. Rapikan dan Kumpulkan 
Simpan konfigurasi seluruh router pada kedua topologi (`copy run start`). Ambil screenshot `do show ip route`, `do show ip eigrp neighbors`/`do show ip ospf neighbor` dari kedua topologi, serta hasil ping PC1-PC2 sebelum dan sesudah link diputus.

## 📊 Tabel Hasil Uji Coba

| No | Pengujian                                                      | Topologi A (EIGRP) | Topologi B (OSPF) |
|----|----------------------------------------------------------------|--------------------|-------------------|
| 1  | Kode route yang muncul pada `show ip route` (D / O)            |                    |                   |
| 2  | Nilai metric pada route menuju LAN tetangga                    |                    |                   |
| 3  | Ping PC1 ke PC2 sebelum link R1–R2 diputus                     |                    |                   |
| 4  | Ping PC1 ke PC2 setelah link R1–R2 diputus                     |                    |                   |
| 5  | Perkiraan waktu hingga ping normal kembali (convergence)       |                    |                   |

## ❓ Pertanyaan Analisis
1. Berdasarkan tabel metric pada Langkah 5, jelaskan perbedaan cara EIGRP dan OSPF dalam menentukan jalur terbaik menuju network yang sama.
2. Berdasarkan hasil Langkah 6, bandingkan proses reconvergence EIGRP (algoritma DUAL, menggunakan feasible successor) dengan OSPF (perhitungan ulang SPF berbasis LSDB) saat link R1-R2 diputus. Protokol mana yang menurutmu lebih cepat pulih, dan mengapa?
   
## 📤 Ketentuan Pengumpulan
- File topologi `.pkt` (berisi kedua topologi dalam satu file) dikumpulkan pada folder `src/`.
- Tabel hasil uji coba dan jawaban pertanyaan analisis dikumpulkan langsung pada file ini.
- Sertakan screenshot `do show ip route` dari kedua topologi serta hasil ping sebelum dan sesudah link diputus.
- Batas waktu pengumpulan:?
