# Soal Praktikum — Pertemuan 2: Administrasi Perangkat Cisco & VLAN

## 📋 Deskripsi Tugas
Praktikan diminta merancang dan mengonfigurasi sebuah jaringan menggunakan Cisco Packet Tracer yang menerapkan segmentasi VLAN, VLAN Trunking, Allowed Trunking, dan sinkronisasi database VLAN melalui VTP (VLAN Trunking Protocol).

## 🧰 Alat & Bahan
- Aplikasi **Cisco Packet Tracer**
- **3 unit Switch** Cisco (Catalyst / manageable)
- **6 unit PC**
- Kabel Cross-Over (penghubung antar-switch) dan kabel Straight (penghubung PC ke switch)

## 🗺️ Topologi Jaringan
Rancang topologi dengan susunan sebagai berikut:

| Perangkat | Peran | Keterangan |
|---|---|---|
| Switch0 | VTP Server | Membuat & mendistribusikan database VLAN |
| Switch1 | VTP Transparent | Meneruskan iklan VTP, tidak menerapkan perubahan ke database lokal |
| Switch2 | VTP Client | Menerima database VLAN dari server |
| PC 1–6 | Host | Terhubung ke port access pada switch sesuai VLAN masing-masing |

Hubungkan Switch0 ↔ Switch1 ↔ Switch2 menggunakan kabel Cross-Over pada port **FastEthernet0/10** di tiap switch, dan konfigurasikan port tersebut sebagai **trunk**.

## 📝 Instruksi Pengerjaan

### Langkah 1. Konfigurasi Dasar
1. Berikan **hostname** yang berbeda pada masing-masing switch (contoh: `Switch0`, `Switch1`, `Switch2`).
2. Atur **enable secret** pada tiap switch sebagai password masuk Privileged Exec Mode.
3. Simpan konfigurasi (`write` / `copy run start`) setelah setiap perubahan.

### Langkah 2. Deklarasi & Akses VLAN
1. Pada **Switch0 (VTP Server)**, buat:
   - **VLAN 10** dengan nama `Marketing`
   - **VLAN 20** dengan nama `Sales`
2. Verifikasi database VLAN menggunakan `show vlan brief`.
3. Daftarkan port **fa0/1** ke VLAN 10 dan port **fa0/3** ke VLAN 20 (access mode).
4. Uji ping dari PC di VLAN 10 ke PC di VLAN 20 — catat hasilnya.

### Langkah 3. VLAN Trunking
1. Konfigurasikan port **FastEthernet0/10** pada setiap switch menjadi mode **trunk**.
2. Verifikasi status trunk dengan `show interface trunk`.
3. Uji ping antar-PC pada VLAN yang sama di switch yang berbeda — catat hasilnya.

### Langkah 4. Allowed Trunking
1. Pada port trunk **FastEthernet0/10**, batasi VLAN yang diizinkan lewat hanya **VLAN 20 (Sales)**.
2. Verifikasi dengan `show interface trunk`, perhatikan baris **Vlans allowed on trunk**.
3. Uji ping antar-PC di VLAN 20 (harus berhasil) dan antar-PC di VLAN 10 (harus RTO) — catat hasilnya.

### Langkah 5. VLAN Trunking Protocol (VTP)
1. Konfigurasikan mode dan parameter VTP pada tiap switch:
   - **Switch0** → mode `server`
   - **Switch1** → mode `transparent`
   - **Switch2** → mode `client`
2. Gunakan domain VTP: `belajar` dan password VTP: `rahasia` pada ketiga switch.
3. Pastikan seluruh port penghubung antar-switch sudah dalam mode **trunk** sebelum mengonfigurasi VTP.
4. Buat kembali VLAN 10 dan VLAN 20 di **Switch0 (Server)**, lalu cek `show vlan brief` di **Switch2 (Client)** untuk memverifikasi sinkronisasi.

## 📊 Tabel Hasil Uji Coba
Isi tabel berikut berdasarkan hasil pengujian yang kamu lakukan.

| No | Pengujian | Hasil (Sukses / RTO) | Keterangan |
|---|---|---|---|
| 1 | Ping PC VLAN 10 → PC VLAN 20 (satu switch) | | |
| 2 | Ping PC VLAN 10 (Switch0) → PC VLAN 10 (Switch2) via trunk | | |
| 3 | Ping PC VLAN 20 → PC VLAN 20 setelah Allowed Trunking VLAN 20 | | |
| 4 | Ping PC VLAN 10 → PC VLAN 10 setelah Allowed Trunking VLAN 20 | | |
| 5 | `show vlan brief` di Switch2 (Client) sebelum VLAN dibuat di Server | | |
| 6 | `show vlan brief` di Switch2 (Client) setelah VLAN dibuat di Server | | |

## ❓ Pertanyaan Analisis
1. Mengapa hasil ping antar-VLAN yang berbeda menghasilkan *Request Timed Out* (RTO)?
2. Apa fungsi utama trunking pada port `FastEthernet0/10` yang menghubungkan antar-switch?
3. Jelaskan efek dari konfigurasi `switchport trunk allowed vlan 20` terhadap lalu lintas VLAN 10.
4. Apakah VLAN 10 dan VLAN 20 yang dibuat di Switch0 (VTP Server) berhasil terdistribusi ke Switch2 (VTP Client)? Jelaskan hasil verifikasinya.
5. Jelaskan mengapa **Switch1 (VTP Transparent)** tidak memiliki VLAN 10 dan VLAN 20 pada database lokalnya meskipun berada dalam domain VTP yang sama.
6. Apa risiko keamanan yang perlu diwaspadai terkait **Revision Number** saat menambahkan switch client baru ke domain VTP aktif?

## 📤 Ketentuan Pengumpulan
- File topologi Cisco Packet Tracer (`.pkt`) dikumpulkan pada folder `src/`.
- Tabel hasil uji coba dan jawaban pertanyaan analisis dikumpulkan dalam format dokumen (Word/PDF) atau langsung pada file ini.
- Sertakan screenshot hasil `show vlan brief`, `show interface trunk`, dan hasil uji ping sebagai bukti pengerjaan.
- Batas waktu pengumpulan: [tanggal]
