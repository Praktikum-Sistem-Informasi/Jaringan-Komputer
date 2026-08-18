# Soal Praktikum — Pertemuan 3: Inter-VLAN Routing (Router-on-a-Stick)

📋 Deskripsi Tugas
Praktikan diminta merancang dan mengonfigurasi sebuah jaringan kampus menggunakan Cisco Packet Tracer yang menerapkan segmentasi VLAN pada tiga ruangan berbeda, kemudian menghubungkan ketiganya menggunakan metode Inter-VLAN Routing Router-on-a-Stick (RoAS), sehingga masing-masing ruangan bisa saling berkomunikasi melalui satu router meski berada di VLAN yang berbeda.

🧰 Alat & Bahan
- Aplikasi Cisco Packet Tracer
- 1 unit Router (mendukung sub-interfac)
- 1 unit Switch (Catalyst / manageable)
- 6 unit PC (2 PC per ruangan)
- Kabel Straight (PC ke switch), Kabel Straight/Copper Cross-Over (switch ke router)

🗺️ Topologi Jaringan
Rancang topologi dengan susunan sebagai berikut:

| Ruangan | VLAN ID | Nama VLAN | Network Address | Gateway (Sub-interface Router) |
|---|---|---|---|---|
| Ruang Dosen | 10 | DOSEN | 192.168.10.0/28 | 192.168.10.1 (Fa0/0.10) |
| Laboratorium | 20 | LAB | 192.168.20.0/28 | 192.168.20.1 (Fa0/0.20) |
| Ruang Tata Usaha (TU) | 30 | TU | 192.168.30.0/28 | 192.168.30.1 (Fa0/0.30) |

Hubungkan PC 1–2 ke port access VLAN 10 (Ruang Dosen), PC 3–4 ke port access VLAN 20 (Lab), dan PC 5–6 ke port access VLAN 30 (TU) pada switch yang sama. Hubungkan Switch ↔ Router menggunakan satu kabel pada port FastEthernet0/1 (switch) ke GigabitEthernet0/0 (router), dan konfigurasikan port switch tersebut sebagai trunk.

📝 Instruksi Pengerjaan

Langkah 1. Konfigurasi Dasar
- Berikan hostname pada switch dan router (contoh: SW-KAMPUS, R-KAMPUS).
- Atur enable secret pada switch dan router sebagai password masuk Privileged Exec Mode.
- Simpan konfigurasi (write / copy run start) setelah setiap perubahan.

Langkah 2. Deklarasi & Akses VLAN
- Pada switch, buat:
  - VLAN 10 dengan nama DOSEN
  - VLAN 20 dengan nama LAB
  - VLAN 30 dengan nama TU
- Verifikasi database VLAN menggunakan `show vlan brief`.
- Daftarkan port ke masing-masing VLAN sesuai ruangan (access mode): PC1–2 → VLAN 10, PC3–4 → VLAN 20, PC5–6 → VLAN 30.
- Uji ping antar-PC dalam VLAN yang sama (misalnya PC1 ke PC2) — catat hasilnya.
- Uji ping dari PC di Ruang Dosen ke PC di Lab — catat hasilnya (harus RTO karena Inter-VLAN Routing belum aktif).

Langkah 3. Konfigurasi Trunk ke Router
- Konfigurasikan port FastEthernet0/1 pada switch (yang mengarah ke router) menjadi mode trunk.
- Izinkan VLAN 10, 20, dan 30 untuk melewati trunk tersebut.
- Verifikasi status trunk dengan `show interface trunk`.

Langkah 4. Konfigurasi Router-on-a-Stick
- Aktifkan interface fisik GigabitEthernet0/0 pada router (`no shutdown`) tanpa memberi alamat IP langsung padanya.
- Buat tiga sub-interface pada GigabitEthernet0/0, masing-masing untuk satu VLAN:
  - Fa0/0.10 → `encapsulation dot1Q 10`, IP 192.168.10.1/28
  - Fa0/0.20 → `encapsulation dot1Q 20`, IP 192.168.20.1/28
  - Fa0/0.30 → `encapsulation dot1Q 30`, IP 192.168.30.1/28
- Verifikasi dengan `show ip interface brief` — pastikan ketiga sub-interface berstatus up/up.

Langkah 5. Konfigurasi IP pada PC & Pengujian Konektivitas Penuh
- Konfigurasikan IP address, subnet mask, dan default gateway pada seluruh PC sesuai ruangannya masing-masing.
- Uji ping dari Ruang Dosen ke Lab, Lab ke TU, dan Ruang Dosen ke TU — seluruhnya harus berhasil (*Reply*) karena RoAS sudah menghubungkan ketiga VLAN.
- Verifikasi tabel routing router dengan `show ip route`, pastikan muncul tiga *connected route* untuk 192.168.10.0/24, 192.168.20.0/24, dan 192.168.30.0/24.

📊 Tabel Hasil Uji Coba
Isi tabel berikut berdasarkan hasil pengujian yang kamu lakukan.

| No | Pengujian | Hasil (Sukses / RTO) | Keterangan |
|---|---|---|---|
| 1 | Ping PC Ruang Dosen → PC Lab (sebelum RoAS dikonfigurasi) | | |
| 2 | Ping PC Ruang Dosen → PC Lab (setelah RoAS dikonfigurasi) | | |
| 3 | Ping PC Lab → PC TU (setelah RoAS dikonfigurasi) | | |
| 4 | Ping PC Ruang Dosen → PC TU (setelah RoAS dikonfigurasi) | | |
| 5 | Jumlah connected route pada `show ip route` di Router | | |
| 6 | Status ketiga sub-interface pada `show ip interface brief` | | |

❓ Pertanyaan Analisis
1. Mengapa pada Langkah 2 ping antar-VLAN menghasilkan Request Timed Out (RTO), padahal PC dalam VLAN yang sama bisa saling ping?
2. Jelaskan fungsi `encapsulation dot1Q <vlan-id>` pada setiap sub-interface router — apa yang terjadi apabila angka VLAN ID pada perintah tersebut tidak sesuai dengan VLAN yang diizinkan pada port trunk switch?
3. Mengapa interface fisik GigabitEthernet0/0 pada router tetap harus dalam kondisi `no shutdown`, meskipun IP address sebenarnya dipasang pada sub-interface, bukan pada interface fisiknya?
4. Pada topologi ini, seluruh trafik dari tiga ruangan (Dosen, Lab, TU) melewati satu kabel fisik yang sama antara switch dan router. Jelaskan risiko/keterbatasan dari kondisi ini, dan apa nama metode Inter-VLAN Routing lain yang bisa mengatasi keterbatasan tersebut!

📤 Ketentuan Pengumpulan
- File topologi Cisco Packet Tracer (.pkt) dikumpulkan pada folder `src/`.
- Tabel hasil uji coba dan jawaban pertanyaan analisis dikumpulkan dalam format dokumen (Word/PDF) atau langsung pada file ini.
- Sertakan screenshot hasil `show vlan brief`, `show interface trunk`, `show ip interface brief`, `show ip route`, dan hasil uji ping sebagai bukti pengerjaan.
- Batas waktu pengumpulan: [tanggal]
