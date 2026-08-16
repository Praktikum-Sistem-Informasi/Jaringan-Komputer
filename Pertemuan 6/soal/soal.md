# Soal Praktikum Singkat, Pertemuan 6: DHCP (Dynamic Host Configuration Protocol)

## 📋 Deskripsi Tugas
Praktikan diminta mengonfigurasi DHCP sederhana pada satu Router untuk melayani satu segmen jaringan. Soal ini dirancang untuk dikerjakan dalam waktu 30 menit di kelas.

## 🧰 Alat & Bahan
- Aplikasi **Cisco Packet Tracer**
- **1 unit Router** Cisco
- **1 unit Switch**
- **3 unit PC**
- Kabel Straight (PC ke switch, switch ke router)

## 🗺️ Topologi Jaringan
Rancang topologi dengan susunan sebagai berikut:

| Perangkat | Peran | Keterangan |
|---|---|---|
| Router | DHCP Server | Melayani permintaan IP dari PC 1, PC 2, dan PC 3 |
| Switch | Penghubung | Menghubungkan Router ke ketiga PC |
| PC 1, PC 2, PC 3 | Host | Diatur ke mode IP Configuration DHCP |

Gunakan pengalamatan berikut sebagai acuan:

| Network | Gateway (interface Router) |
|---|---|
| 192.168.10.0/24 | 192.168.10.1 |

## 📝 Instruksi Pengerjaan

### Langkah 1. Konfigurasi Dasar (5 menit)
1. Berikan **hostname** pada Router.
2. Konfigurasikan IP address statis pada interface Router yang terhubung ke Switch, sesuai gateway di atas.
3. Pastikan interface dalam kondisi aktif (`no shutdown`).

### Langkah 2. Konfigurasi DHCP pada Router (10 menit)
1. Buat pool DHCP menggunakan `ip dhcp pool [nama_pool]`.
2. Tentukan `network`, `default-router`, dan `dns-server` sesuai pengalamatan yang direncanakan.
3. Verifikasi pool yang sudah dibuat dengan `show ip dhcp pool`.

### Langkah 3. Uji Koneksi pada Sisi Client (10 menit)
1. Atur ketiga PC ke mode IP Configuration **DHCP**.
2. Jalankan `ipconfig` pada tiap PC untuk melihat IP yang diterima, lalu catat hasilnya.
3. Jalankan `show ip dhcp binding` pada Router untuk memastikan ketiga PC tercatat.

### Langkah 4. Rapikan dan Kumpulkan (5 menit)
1. Simpan konfigurasi (`write` atau `copy run start`).
2. Ambil screenshot hasil `ipconfig` dan `show ip dhcp binding`.

## 📊 Tabel Hasil Uji Coba

| No | Pengujian | Hasil | Keterangan |
|---|---|---|---|
| 1 | IP yang diterima PC 1 setelah `ipconfig` | | |
| 2 | IP yang diterima PC 2 setelah `ipconfig` | | |
| 3 | IP yang diterima PC 3 setelah `ipconfig` | | |
| 4 | `show ip dhcp binding` pada Router menampilkan ketiga PC | | |

## ❓ Pertanyaan Analisis
1. Jelaskan proses DORA yang terjadi saat PC pertama kali meminta IP address ke DHCP.
2. Apa fungsi perintah `default-router` dan `dns-server` di dalam pool DHCP?

## 📤 Ketentuan Pengumpulan
- File topologi Cisco Packet Tracer (`.pkt`) dikumpulkan pada folder `src/`.
- Tabel hasil uji coba dan jawaban pertanyaan analisis dikumpulkan langsung pada file ini.
- Sertakan screenshot hasil `ipconfig` tiap PC dan `show ip dhcp binding` pada Router sebagai bukti pengerjaan.
- Batas waktu pengumpulan: akhir sesi kelas hari ini.
