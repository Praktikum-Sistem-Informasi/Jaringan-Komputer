# Pertemuan 2: Administrasi Perangkat Cisco & Virtual LAN (VLAN)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami hierarki mode konfigurasi pada CLI perangkat Switch Cisco.
- Praktikan mampu melakukan konfigurasi dasar identitas (hostname) dan keamanan (password) perangkat Cisco.
- Praktikan mampu mendeklarasikan VLAN dan mendaftarkan port switch ke segmen VLAN yang sesuai.
- Praktikan mampu menghubungkan lalu lintas VLAN antar-switch fisik melalui konfigurasi VLAN Trunking.
- Praktikan mampu membatasi hak akses VLAN melewati jalur trunk menggunakan fitur Allowed Trunking.
- Praktikan mampu mengonfigurasi dan mengotomatisasi database VLAN menggunakan VLAN Trunking Protocol (VTP).

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: rancangan topologi 3 Switch & 6 PC, serta instruksi konfigurasi VTP Server/Transparent/Client.
- `docs/` — berisi modul ini beserta materi pendukung lain (cheatsheet CLI, referensi VLAN/VTP).

## 🚀 Cara Menjalankan
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
# 1. Buka file topologi pada Cisco Packet Tracer (src/topologi.pkt)
# 2. Klik perangkat Switch, buka tab CLI, lalu masukkan perintah konfigurasi, contoh:
Switch> enable
Switch# configure terminal
Switch(config)# hostname Switch0
```

## 📖 Materi Praktikum

### 1. Konfigurasi Dasar & CLI Cisco

**Hierarki mode CLI Cisco:**
- **User Exec Mode (`Switch>`)** — level pertama saat mengakses perangkat; hanya bisa melihat informasi dasar tanpa hak melakukan perubahan konfigurasi.
- **Privileged Exec Mode (`Switch#`)** — untuk melihat informasi detail sistem seperti tabel routing, interface, protokol, dan konfigurasi berjalan. Masuk dengan perintah `enable`.
- **Global Configuration Mode (`Switch(config)#`)** — mode utama untuk perubahan konfigurasi global seperti hostname, password, pembuatan user, dan lainnya. Masuk dengan perintah `configure terminal`.
- **Interface Configuration Mode (`Switch(config-if)#`)** — sub-mode untuk memodifikasi parameter port interface tertentu. Masuk dengan perintah `interface [nama_interface]`.

**Cheatsheet CLI Konfigurasi Dasar:**

| Fungsi | Perintah CLI | Penjelasan |
|---|---|---|
| Masuk Privilege Mode | `Switch> enable` | Berpindah ke level pemeriksaan sistem. |
| Masuk Global Config | `Switch# configure terminal` | Berpindah ke mode konfigurasi global. |
| Mengubah Hostname | `Switch(config)# hostname [Nama_Baru]` | Mengubah identitas nama perangkat. |
| Menyimpan Konfigurasi | `Switch# write` atau `copy run start` | Menyimpan konfigurasi aktif (running-config) ke NVRAM (startup-config) agar tidak hilang saat perangkat mati (reboot). |
| Mereset Konfigurasi | `Switch# write erase` | Menghapus seluruh file konfigurasi kembali ke pengaturan pabrik. |
| Melihat Status Port | `Switch# show ip interface brief` | Menampilkan ringkasan status fisik dan logis (up/down) seluruh interface. |
| Melihat Konfigurasi | `Switch# show running-config` | Menampilkan file konfigurasi yang sedang berjalan di RAM. |

**Konfigurasi Keamanan (Password):**
Pemberian autentikasi diperlukan agar tidak sembarang orang dapat mengonfigurasi perangkat.
- Password konsol digunakan saat ada yang mengakses perangkat lewat port konsol fisik.
- Password masuk Privilege Mode:
  ```
  Switch(config)# enable password coba1   # teks biasa, tidak terenkripsi
  Switch(config)# enable secret coba2     # terenkripsi kuat
  ```
  > Jika keduanya dikonfigurasi, sistem Cisco secara otomatis akan memprioritaskan penggunaan `enable secret` karena faktor keamanan enkripsi.

### 2. Deklarasi & Akses VLAN

Virtual LAN (VLAN) adalah metode untuk membagi satu jaringan fisik menjadi beberapa segmen jaringan logis (virtual) yang terpisah. Perangkat hanya dapat berkomunikasi dengan perangkat lain yang berada di dalam satu VLAN yang sama. VLAN membantu mengisolasi lalu lintas data, meningkatkan keamanan jaringan (security), serta mengefisiensikan bandwidth. VLAN hanya didukung pada switch manageable (seperti Cisco Catalyst) dan tidak didukung pada switch unmanageable.

**Skenario:** Buat VLAN 10 dengan nama Marketing dan VLAN 20 dengan nama Sales, lalu daftarkan port interface komputer ke VLAN tersebut.

Langkah kerja:
1. **Deklarasi VLAN ID dan Nama** pada Global Configuration Mode.
2. **Verifikasi Database VLAN** — gunakan perintah `Switch# show vlan brief` untuk memastikan VLAN telah terdaftar.
3. **Mendaftarkan Port Switch ke VLAN (Access Mode)** — daftarkan port `fa0/1` ke VLAN 10 dan port `fa0/3` ke VLAN 20.
4. **Uji Koneksi** — lakukan tes ping dari komputer di VLAN 10 ke komputer di VLAN 20. Hasilnya harus *Request Timed Out* (RTO) karena berada di VLAN yang berbeda dan lalu lintasnya terisolasi.

### 3. VLAN Trunking

Jika komputer di VLAN 10 pada Switch Gedung 1 ingin terhubung dengan komputer di VLAN 10 pada Switch Gedung 2, dibutuhkan jalur **Trunking**. Trunking membawa lalu lintas data dari berbagai VLAN yang berbeda melalui satu koneksi kabel fisik yang sama secara bersamaan, sehingga menghemat kabel fisik dan mengoptimalkan bandwidth. Protokol enkapsulasi trunking standar industri terbuka yang digunakan adalah **IEEE 802.1Q (dot1q)**, yang menyisipkan tag 4-byte pada frame data.

**Langkah kerja:** Hubungkan kedua switch menggunakan kabel Cross-Over pada port FastEthernet0/10, lalu ubah port penghubung tersebut ke mode Trunk pada kedua switch:
```
Switch(config)# interface FastEthernet0/10
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
```

**Uji Verifikasi:** Ketikkan perintah `show interface trunk` pada switch untuk memastikan port `Fa0/10` telah sukses beroperasi dalam status *trunking*. Setelah trunk aktif, komputer di VLAN yang sama antar-switch akan sukses terhubung (*ping reply*).

### 4. Allowed Trunking

Secara default, sebuah port trunk akan mengizinkan seluruh VLAN (ID 1–1005) untuk lewat. **Allowed Trunking** adalah fitur pengontrol lalu lintas data untuk menetapkan daftar spesifik VLAN ID mana saja yang diizinkan melewati jalur komunikasi trunk tersebut. Dengan menerapkan fitur ini, lalu lintas data dari VLAN yang tidak ada di dalam daftar secara otomatis akan diblokir demi efisiensi bandwidth dan memperketat keamanan jaringan.

**Skenario:** Konfigurasikan port trunk `FastEthernet0/10` agar hanya mengizinkan lalu lintas VLAN 20 (Sales) saja, sedangkan VLAN lain diblokir.

```
Switch(config)# interface FastEthernet0/10
Switch(config-if)# switchport trunk allowed vlan 20
Switch(config-if)# exit
```

**Uji Verifikasi:**
1. Jalankan perintah verifikasi: `Switch# show interface trunk`.
2. Perhatikan baris **Vlans allowed on trunk** — port harus menunjukkan angka **20** saja.
3. Lakukan tes ping antar-PC di VLAN 20 (harus sukses), lalu uji ping antar-PC di VLAN 10 (harus gagal/RTO karena telah diblokir di jalur trunk).

### 5. VLAN Trunking Protocol (VTP)

Pada jaringan berskala besar dengan puluhan switch, konfigurasi pembuatan VLAN satu per satu secara manual sangat tidak efisien. **VTP (VLAN Trunking Protocol)** adalah protokol *Cisco Proprietary* (hak milik Cisco) yang memungkinkan administrator mengelola pembuatan, penghapusan, dan pengubahan nama VLAN secara terpusat dari satu switch utama. Konfigurasi VTP disimpan dalam file `vlan.dat` pada memori flash.

**VTP membagi switch ke dalam 3 peran (mode):**
1. **VTP Server (default)** — membuat, mengubah, atau menghapus VLAN serta menyebarkan pembaruan database (*advertisement*) ke seluruh switch klien.
2. **VTP Client** — menerima dan menerapkan perubahan database VLAN dari server; tidak dapat membuat atau memodifikasi VLAN secara lokal.
3. **VTP Transparent** — berfungsi sebagai penyalur, meneruskan iklan VTP ke switch lain tetapi tidak menerapkan perubahan tersebut pada database internalnya sendiri.

> ⚠️ **Peringatan Keamanan:** Setiap pembaruan ditandai dengan **Revision Number**. Jika switch client dengan nomor revisi lebih tinggi dimasukkan ke jaringan, database VLAN server dapat terhapus secara otomatis. Selalu reset switch sebelum menghubungkannya kembali ke domain VTP aktif.

**Langkah Kerja Konfigurasi VTP** *(syarat utama: interface penghubung antar-switch sudah dikonfigurasi dalam mode Trunk)*:

Konfigurasi Switch Server:
```
Switch0(config)# vtp mode server
Switch0(config)# vtp domain belajar
Switch0(config)# vtp password rahasia
```

Konfigurasi Switch Transparent:
```
Switch1(config)# vtp mode transparent
Switch1(config)# vtp domain belajar
Switch1(config)# vtp password rahasia
```

Konfigurasi Switch Client:
```
Switch2(config)# vtp mode client
Switch2(config)# vtp domain belajar
Switch2(config)# vtp password rahasia
```

**Uji Verifikasi:** Buat VLAN baru (misal VLAN 10 dan 20) di Switch Server, lalu jalankan perintah `#show vlan brief` di Switch Client. Jika database VLAN 10 dan 20 otomatis tersinkronisasi di switch client, maka konfigurasi VTP telah berhasil.

### 6. Tugas & Evaluasi Praktikum
1. Rancang topologi di Cisco Packet Tracer menggunakan 3 Switch dan 6 PC!
2. Konfigurasikan switch pertama sebagai VTP Server, switch kedua sebagai VTP Transparent, dan switch ketiga sebagai VTP Client!
3. Buat VLAN 10 (Marketing) dan VLAN 20 (Sales) di Switch Server!
4. Lakukan verifikasi, apakah VLAN tersebut sukses didistribusikan ke VTP Client? Jelaskan analisis Anda mengapa switch transparent tidak memiliki VLAN tersebut!

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer — Pertemuan 2: Administrasi Perangkat Cisco & Virtual LAN (VLAN)
