# Soal Praktikum Singkat, Pertemuan 6: DHCP, DNS, dan Web Server

## 📋 Deskripsi Tugas
Praktikan diminta mengonfigurasi satu topologi sederhana yang menggabungkan tiga layanan sekaligus: DHCP (di Router), DNS, dan Web Server (keduanya di Server), lalu membuktikan bahwa client bisa mengakses sebuah halaman web hanya dengan mengetik nama domain di browser.

## 🧰 Alat & Bahan
- Aplikasi **Cisco Packet Tracer**
- **1 unit Router** Cisco
- **1 unit Server**
- **1 unit Switch**
- **3 unit PC**
- Kabel Straight (PC ke switch, switch ke router dan server)

## 🗺️ Topologi Jaringan

| Perangkat | Peran | Keterangan |
|---|---|---|
| Router | DHCP Server | Melayani permintaan IP dari PC 1, PC 2, dan PC 3 |
| Server | DNS dan Web Server | Menjawab query domain, sekaligus menyajikan halaman web |
| Switch | Penghubung | Menghubungkan Router, Server, dan ketiga PC |
| PC 1, PC 2, PC 3 | Host | Diatur ke mode IP Configuration DHCP |

Gunakan pengalamatan berikut sebagai acuan:

| Network | Gateway (interface Router) | IP Server |
|---|---|---|
| 192.168.10.0/24 | 192.168.10.1 | 192.168.10.2 (statis) |

## 📝 Instruksi Pengerjaan

### Langkah 1. Konfigurasi Dasar (5 menit)
1. Berikan **hostname** pada Router, lalu konfigurasikan IP address statis pada interface yang terhubung ke Switch sesuai gateway di atas.
2. Berikan IP address statis pada Server, jangan gunakan DHCP untuk Server itu sendiri.

### Langkah 2. Konfigurasi DHCP pada Router (5 menit)
1. Buat pool DHCP menggunakan `ip dhcp pool [nama_pool]`.
2. Tentukan `network`, `default-router`, dan `dns-server` (isi dengan IP Server, karena Server yang akan menjalankan DNS).
3. Verifikasi dengan `show ip dhcp pool`.

### Langkah 3. Konfigurasi DNS pada Server (5 menit)
1. Buka Server, tab **Config**, pilih layanan **DNS**, lalu aktifkan Service: On.
2. Tambahkan satu **A Record**: isi Name dengan nama domain bebas (misalnya `praktisi.web.id`), dan Address dengan IP Server itu sendiri.
3. Klik **Add** untuk menyimpan record.

### Langkah 4. Konfigurasi Web Server (5 menit)
1. Masih pada Server, buka layanan **HTTP**, lalu aktifkan Service: On.
2. Buka file `index.html`, ubah sedikit isinya (misalnya tambahkan nama kelompok), lalu simpan.

### Langkah 5. Uji Koneksi pada Sisi Client (10 menit)
1. Atur ketiga PC ke mode IP Configuration **DHCP**, lalu jalankan `ipconfig` untuk memastikan IP dan DNS server sudah diterima.
2. Buka **Web Browser** pada salah satu PC, ketik nama domain yang sudah didaftarkan di Langkah 3.
3. Pastikan halaman `index.html` yang sudah diubah tadi berhasil tampil.

### Langkah 6. Rapikan dan Kumpulkan (5 menit)
1. Simpan konfigurasi Router (`write` atau `copy run start`).
2. Ambil screenshot hasil `ipconfig`, tampilan browser yang berhasil membuka domain, dan konfigurasi DNS/HTTP pada Server.

## 📊 Tabel Hasil Uji Coba

| No | Pengujian | Hasil | Keterangan |
|---|---|---|---|
| 1 | IP dan DNS server yang diterima PC setelah `ipconfig` | | |
| 2 | Domain berhasil dibuka lewat Web Browser | | |
| 3 | Isi halaman web sesuai perubahan yang dilakukan pada `index.html` | | |

## ❓ Pertanyaan Analisis
1. Urutkan dan jelaskan secara singkat apa yang terjadi mulai dari PC menyalakan browser dan mengetik nama domain, sampai halaman web akhirnya tampil di layar.
2. Apa yang akan terjadi jika Server tidak diberi IP address statis, melainkan ikut mengambil IP dari DHCP?

## 📤 Ketentuan Pengumpulan
- File topologi Cisco Packet Tracer (`.pkt`) dikumpulkan pada folder `src/`.
- Tabel hasil uji coba dan jawaban pertanyaan analisis dikumpulkan langsung pada file ini.
- Sertakan screenshot hasil `ipconfig`, tampilan browser, dan konfigurasi DNS/HTTP pada Server sebagai bukti pengerjaan.
- Batas waktu pengumpulan: akhir sesi kelas hari ini.
