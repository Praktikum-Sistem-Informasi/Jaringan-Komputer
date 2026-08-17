# Soal Praktikum — Pertemuan 7: Konfigurasi Telnet pada Switch

## 📋 Deskripsi Tugas
Praktikan diminta mengonfigurasi akses remote menggunakan **Telnet** pada sebuah Switch Cisco melalui Cisco Packet Tracer, lalu menguji koneksi Telnet tersebut dari PC.

## 🧰 Alat & Bahan
- Aplikasi **Cisco Packet Tracer**
- **1 unit Switch** Cisco (Catalyst / manageable)
- **1 unit PC**
- Kabel Straight (penghubung PC ke switch)

## 🗺️ Topologi Jaringan
Rancang topologi dengan susunan sebagai berikut:

| Perangkat | Peran | Keterangan |
|---|---|---|
| Switch0 | Perangkat yang diakses remote | Diberi IP pada interface VLAN 1 |
| PC0 | Host / client | Mengakses Switch0 melalui Telnet |

Hubungkan PC0 ↔ Switch0 menggunakan kabel Straight.

## 📝 Instruksi Pengerjaan

### Langkah 1. Konfigurasi IP Switch
1. Masuk ke konfigurasi **interface VLAN 1** pada Switch0.
2. Berikan IP address bebas (contoh: `192.168.1.1/24`).
3. Aktifkan interface dengan `no shutdown`.

### Langkah 2. Konfigurasi Telnet
1. Masuk ke `line vty 0 4`.
2. Batasi protokol remote access hanya Telnet (`transport input telnet`).
3. Atur password bebas (contoh: `12345`) dan aktifkan `login`.

### Langkah 3. Uji Koneksi
1. Berikan IP address pada PC0 satu subnet dengan Switch0.
2. Buka **Command Prompt** di PC0.
3. Jalankan `telnet <ip_switch>`.
4. Screenshot hasilnya (harus muncul prompt `Password:`).

## 📤 Ketentuan Pengumpulan
- Kumpulkan tugas melalui **Google Form** yang telah dibagikan.
- Lampirkan screenshot **proses konfigurasi CLI pada Switch** (terlihat command yang diketik beserta prompt-nya).
- Lampirkan screenshot hasil uji koneksi Telnet
