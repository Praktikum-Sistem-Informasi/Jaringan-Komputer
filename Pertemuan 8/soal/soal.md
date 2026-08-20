# Soal Praktikum — Pertemuan 8: Konfigurasi Layanan Server (SMTP/POP3 & DNS)

## 📋 Deskripsi Tugas
Praktikan diminta membangun topologi jaringan sederhana menggunakan Cisco Packet Tracer, mengonfigurasi IP address pada seluruh perangkat, lalu mengaktifkan layanan **SMTP/POP3** dan **DNS** pada Server.

## 🧰 Alat & Bahan
- Aplikasi **Cisco Packet Tracer**
- **1 unit Server**
- **2 unit PC**
- **1 unit Switch**
- Kabel Straight (penghubung PC/Server ke Switch)

## 🗺️ Topologi Jaringan
Rancang topologi dengan susunan sebagai berikut:

| Perangkat | Peran | Keterangan |
|---|---|---|
| Server0 | Penyedia layanan | Menjalankan layanan SMTP/POP3 dan DNS |
| PC0 | Host / client | Terhubung ke jaringan melalui Switch |
| PC1 | Host / client | Terhubung ke jaringan melalui Switch |
| Switch0 | Perangkat penghubung | Menghubungkan Server0, PC0, dan PC1 |

Hubungkan Server0 ↔ Switch0, PC0 ↔ Switch0, dan PC1 ↔ Switch0 menggunakan kabel Straight.

## 📝 Instruksi Pengerjaan

### Langkah 1. Membangun Topologi
1. Tambahkan 1 unit **Server**, 2 unit **PC**, dan 1 unit **Switch** ke workspace.
2. Hubungkan Server dan kedua PC ke Switch menggunakan kabel **Straight**.
3. Pastikan seluruh port terhubung (indikator link berwarna hijau).

### Langkah 2. Konfigurasi IP Address
1. Buka tab **Desktop > IP Configuration** pada Server dan masing-masing PC.
2. Berikan IP address satu subnet untuk seluruh perangkat, contoh:
   - Server0: `192.168.1.1/24`
   - PC0: `192.168.1.2/24`
   - PC1: `192.168.1.3/24`
3. Pastikan DNS pada pc sesuai dengan ip server.

### Langkah 3. Konfigurasi Layanan DNS
1. Masuk ke tab **Services > DNS** pada Server0.
2. Aktifkan (**On**) layanan DNS.
3. Tambahkan record, misalnya:
   - Type: `A Record`
   - Name: `praktikum.com`
   - IP Address: `192.168.1.1`
4. Klik **Add**, lalu simpan.

### Langkah 4. Konfigurasi Layanan SMTP/POP3
1. Masuk ke tab **Services > EMAIL** pada Server0.
2. Aktifkan (**On**) layanan **SMTP** dan **POP3**.
3. Isi **Domain Name**: `praktikum.com`, klik **Set**.
4. Buat akun pengguna email, contoh:
   - User1: `pc0` / password bebas
   - User2: `pc1` / password bebas

## 📤 Ketentuan Pengumpulan
- Kumpulkan tugas melalui **Google Form** yang telah dibagikan.
- Lampirkan screenshot **topologi jaringan** yang telah dibuat.
- Lampirkan screenshot **konfigurasi IP** pada Server dan kedua PC.
- Lampirkan screenshot **konfigurasi DNS dan SMTP/POP3** pada Server.
