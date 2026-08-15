# Pertemuan 7: Konfigurasi WLAN, Keamanan CLI, dan Remote Access

## 🎯 Tujuan Pembelajaran
- Memahami konsep dasar jaringan nirkabel (WLAN) dan cara kerja Access Point dalam menyediakan koneksi wireless.
- Mengonfigurasi Access Point, meliputi pengaturan nama jaringan (SSID) dan keamanan jaringan menggunakan WPA2-PSK.
- Menghubungkan perangkat client seperti smartphone atau laptop ke jaringan WLAN dengan konfigurasi SSID dan password yang sesuai.
- Memastikan client mendapatkan alamat IP secara otomatis melalui layanan DHCP.
- Menerapkan keamanan dasar pada Cisco Router, khususnya pengamanan akses console dan Privilege Mode.
- Memahami dan mengonfigurasi remote access menggunakan Telnet dan SSH.
- Membedakan Telnet dan SSH, terutama dari aspek keamanan dan enkripsi komunikasi.

## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: ?
- `docs/` — berisi modul ini beserta materi pendukung lain (?).

## 🚀 Cara Menjalankan Cisco Packet Trace
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
1. Buka aplikasi Cisco Packet Tracer (matikan jaringan internet).
2. Buat topologi jaringan baru sesuai intruksi
3. Hubungkan perangkat sesuai topologi (gunakan kabel yang sesuai ).
4. Klik masing-masing perangkat untuk membuka menu konfigurasi 
   (tab Config/GUI/CLI) sesuai intruksi.
5. Simpan file .pkt secara berkala.
```

## 📖 Materi Praktikum

### 1. WLAN (Wireless Local Area Network)
 
WLAN (Wireless Local Area Network) adalah sistem jaringan komputer yang mencakup area lokal tertentu seperti gedung perkantoran, kampus, atau rumah tanpa menggunakan sambungan kabel fisik untuk menghubungkan perangkat-perangkat di dalamnya. Sebagai gantinya, WLAN memanfaatkan teknologi frekuensi radio untuk mengirimkan dan menerima data melalui medium udara. Frekuensi yang paling umum digunakan berada pada pita gelombang radio 2,4 GHz dan 5 GHz, yang beroperasi berdasarkan standar teknis IEEE 802.11, atau yang secara komersial lebih akrab kita sebut dengan Wi-Fi.
 
Dalam cara kerjanya, infrastruktur WLAN sangat mengandalkan perangkat pusat yang disebut Access Point (AP) atau wireless router. Perangkat ini berfungsi sebagai stasiun pemancar dan penerima (transceiver) yang menjembatani lalu lintas data antara perangkat nirkabel pengguna — seperti laptop, tablet, dan ponsel pintar — dengan jaringan kabel utama atau koneksi penyedia internet (ISP). Ketika pengguna berpindah tempat di dalam batas area cakupan sinyal Access Point tersebut, koneksi perangkat akan tetap terjaga secara otomatis. Hal ini memberikan tingkat mobilitas, skalabilitas, dan fleksibilitas tinggi yang tidak bisa ditawarkan oleh jaringan berbasis kabel (LAN) tradisional.
 
#### Konfigurasi WLAN
 
<img width="940" height="316" alt="Menu konfigurasi fisik/GUI Access Point" src="https://github.com/user-attachments/assets/a4efeda3-93e6-4fcb-b65f-24258bfdeeab" />

---
Secara bawaan (default), jaringan pada Access Point terbuka tanpa keamanan. Oleh karena itu, kita perlu mengatur konfigurasi SSID, sandi, dan jenis enkripsinya seperti pada gambar berikut:

<img width="320" height="258" alt="image" src="https://github.com/user-attachments/assets/d61a1077-718f-4845-a29a-b941f67ae841" /> 

<br>

<img width="573" height="297" alt="Konfigurasi SSID dan enkripsi WPA2-PSK pada Access Point" src="https://github.com/user-attachments/assets/97c61a18-4e2e-4905-be48-3fbf1b8fc46f" />

---
Langkah selanjutnya, buka menu Config pada Smartphone dan pilih antarmuka Wireless0. Hubungkan perangkat ke jaringan Access Point dengan memasukkan kata sandi yang telah dikonfigurasi sebelumnya, seperti pada gambar berikut:

 <img width="573" height="551" alt="image" src="https://github.com/user-attachments/assets/2794f37d-7fa3-4188-bd31-5c59b6d29b27" />

---
Berbeda dengan perangkat Smartphone yang sudah memiliki fitur nirkabel bawaan, pada perangkat Laptop kita harus menyesuaikan modul fisiknya terlebih dahulu. Masuk ke tab Physical, matikan daya laptop, lepaskan modul LAN bawaan, dan ganti dengan modul wireless WPC300N seperti pada gambar berikut:

<img width="576" height="303" alt="image" src="https://github.com/user-attachments/assets/f7ff1660-355c-4823-a661-fba1aa642b9c" />

---
Langkah selanjutnya, bisa melakukan konfigurasi seperti pada smartphone :

<img width="573" height="551" alt="image" src="https://github.com/user-attachments/assets/6611b8eb-1eee-486b-94ac-61a98c554fde" />


### 2. Keamanan CLI (Command Line Interface)

Ini konsep dasar mengamankan akses ke router/switch Cisco supaya nggak sembarang orang bisa masuk dan konfigurasi perangkat:


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
2. Perhatikan baris **Vlans allowed on trunk** port harus menunjukkan angka **20** saja.
3. Lakukan tes ping antar-PC di VLAN 20 (harus sukses), lalu uji ping antar-PC di VLAN 10 (harus gagal/RTO karena telah diblokir di jalur trunk).

### 5. VLAN Trunking Protocol (VTP)

Pada jaringan berskala besar dengan puluhan switch, konfigurasi pembuatan VLAN satu per satu secara manual sangat tidak efisien. **VTP (VLAN Trunking Protocol)** adalah protokol *Cisco Proprietary* (hak milik Cisco) yang memungkinkan administrator mengelola pembuatan, penghapusan, dan pengubahan nama VLAN secara terpusat dari satu switch utama. Konfigurasi VTP disimpan dalam file `vlan.dat` pada memori flash.

**VTP membagi switch ke dalam 3 peran (mode):**
1. **VTP Server (default)** membuat, mengubah, atau menghapus VLAN serta menyebarkan pembaruan database (*advertisement*) ke seluruh switch klien.
2. **VTP Client** menerima dan menerapkan perubahan database VLAN dari server; tidak dapat membuat atau memodifikasi VLAN secara lokal.
3. **VTP Transparent** berfungsi sebagai penyalur, meneruskan iklan VTP ke switch lain tetapi tidak menerapkan perubahan tersebut pada database internalnya sendiri.

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
