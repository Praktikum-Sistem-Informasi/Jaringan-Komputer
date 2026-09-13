# Administrasi  Perangkat Cisco &  Virtual LAN (VLAN)

### Konfigurasi Dasar &  CLI Cisco

```
Switch>Enable 
```

Masuk Privillege Mode

Fungsinya memberi akses untuk :

- Melihat konfigurasi lengkap (`show running-config`, `show ip interface`, dll)
- Masuk ke Global Configuration Mode (`configure terminal`) untuk mengubah setting
- Menjalankan perintah debug, reload, copy config, dan perintah administratif lainnya

```
Switch>Configurate Terminal
```

Masuk Global Config

Gunanya untuk a melakukan konfigurasi global yang mempengaruhi keseluruhan device, seperti : 

- Ganti hostname (`hostname NamaSwitch`)
- Bikin VLAN (`vlan 10`)
- Setting password (`enable secret cisco`)
- Masuk ke sub-mode lain seperti interface config atau line config

### Virtual LAN (VLAN)

VLAN (Virtual Local Area Network) adalah sub network yang dapat mengelompokkan kumpulan perangkat pada jaringan area lokal fisik (LAN) yang terpisah. Vlan juga bisa dikatakan pengelompokkan logis perangkat dalam domain siaran yang sama.

##### Fungsi

Fungsi *Virtual Local Area Network* pada jaringan komputer adalah menyediakan metode pada jaringan yang dapat membagi jaringan fisik menjadi beberapa broadcast domain. 

1. Pengurangan Biaya

2. Menawarkan lebih banyak fleksibilitas daripada solusi jaringan non-virtual

3. Mengatasi jumlah administratif yang dibutuhkan.

##### Tujuan

1. Mengurangi Lalu Lintas Jaringan yang Berlebihan

Dengan membagi jaringan fisik menjadi beberapa broadcast domain, traffic tidak menumpuk di satu segmen besar

2. Mengurangi Collision (Tabrakan Data)

Meminimalisir tabrakan paket yang terjadi saat beberapa workstation mengirim data bersamaan dan Mencegah tabrakan menyebar ke seluruh jaringan yang bikin performa lambat.

3. Membatasi Broadcast Domain

Paket broadcast dari satu workstation hanya sampai ke perangkat dalam VLAN yang sama, tidak menyebar ke seluruh jaringan fisik dan Ini bikin jaringan lebih efisien karena gak semua perangkat harus "dengar" broadcast yang gak relevan buat mereka.

4. Meningkatkan Keamanan Data

Karena VLAN memisahkan traffic secara logis, data di satu VLAN gak bisa diakses langsung sama perangkat di VLAN lain tanpa melalui routing, Cocok buat memisahkan data sensitif (misal: VLAN khusus Finance terpisah dari VLAN Umum).

5. Fleksibilitas Pengelompokan Logis

Perangkat bisa dikelompokkan berdasarkan **fungsi/departemen** (misal: HR, IT, Finance), bukan berdasarkan lokasi fisik, Memudahkan manajemen jaringan skala besar, terutama di perusahaan dengan banyak divisi.

6. Efisiensi Manajemen Jaringan

Admin bisa mengatur ulang jaringan (misal pindah user ke departemen lain) cukup lewat konfigurasi switch, tanpa perlu menata ulang kabel fisik.

```
Cara  membuat vlan :
Switch#vlan (id)
Switch (config-if)# name (Nama Vlan)

Caraa menghapus  vlan yang sudah ada:
Switch#no vlan (id)
```

### Vlan Trunking

Trunk adalah link (koneksi) antara dua switch (atau switch-router) yang bisa membawa traffic dari banyak VLAN sekaligus dalam satu kabel fisik.

Normalnya, satu port switch cuma bisa jadi anggota satu VLAN (disebut access port). Tapi kalau kamu punya VLAN 10, 20, 30 yang tersebar di beberapa switch, kamu gak mau tarik kabel terpisah untuk tiap VLAN antar switch — boros dan gak efisien. Solusinya pakai trunk link.

Cara Kerja:

- Setiap frame yang lewat trunk akan diberi **tag** (label) yang menandakan dia berasal dari VLAN mana
- Standar tagging yang umum dipakai: **IEEE 802.1Q**
- Switch penerima akan membaca tag ini dan tahu harus meneruskan frame ke VLAN mana

```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
```

### Allowed Trunking

Secara default, saat sebuah port dijadikan trunk, semua VLAN (VLAN 1–4094) diizinkan lewat trunk tersebut. Tapi kadang kita gak mau semua VLAN ikut lewat — misal karena alasan keamanan atau efisiensi traffic. Di sinilah fungsi allowed VLAN list — untuk membatasi VLAN mana saja yang boleh melewati trunk tertentu.

```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

Artinya: hanya VLAN 10, 20, dan 30 yang diizinkan lewat trunk ini — VLAN lain (misalnya VLAN 40) tidak akan diteruskan meskipun trunk aktif.

| Perintah                                | Fungsi                                  |
| --------------------------------------- | --------------------------------------- |
| switchport trunk allowed vlan 10,20,30  | Set VLAN yang diizinkan (replace semua) |
| switchport trunk allowed vlan add 40    | Tambah VLAN 40 ke daftar yang sudah ada |
| switchport trunk allowed vlan remove 20 | Hapus VLAN 20 dari daftar               |
| switchport trunk allowed vlan all       | Izinkan semua VLAN (default)            |

### Vlan Trunking Protocol (VTP)

##### Pengertian

VTP adalah protokol proprietary milik Cisco yang berfungsi untuk menyebarkan dan menyinkronkan informasi konfigurasi VLAN di seluruh switch dalam satu domain VTP. VTP bekerja pada layer 2 OSI dan memanfaatkan trunk link untuk menyebarkan informasi VLAN antar switch, meliputi VLAN ID, nama VLAN, tipe VLAN, dan informasi konfigurasi lain yang terkait. 

Kapan perlu digunakan? VTP cocok dipakai kalau jaringanmu punya banyak switch Cisco dan sering ada perubahan VLAN — jadi tidak perlu mengonfigurasi VLAN satu per satu di tiap switch. Kalau jaringan kecil (2-3 switch) atau VLAN jarang be    rubah, biasanya lebih aman pakai mode transparent atau tidak pakai VTP sama sekali, supaya risiko kesalahan sinkronisasi lebih kecil.

##### Fungsi

- Menyederhanakan pengelolaan VLAN — administrator hanya perlu mengonfigurasi VLAN pada satu switch (biasanya VTP Server), lalu konfigurasi tersebut otomatis tersebar ke switch lain dalam domain yang sama.

- Menjaga konsistensi konfigurasi VLAN — semua switch dalam domain VTP memiliki database VLAN yang sama, sehingga menghindari inkonsistensi yang bisa menyebabkan masalah komunikasi antar VLAN.

- Otomatisasi penyebaran (advertise) informasi VLAN — perubahan VLAN (tambah, ubah, hapus) di switch server langsung disebarkan ke switch client tanpa perlu konfigurasi manual di tiap perangkat.

- Plug and Play VLAN — switch baru yang bergabung ke domain VTP otomatis mendapatkan konfigurasi VLAN yang sudah ada, tanpa perlu dikonfigurasi manual.

- Menghemat waktu dan mengurangi human error — sangat membantu di jaringan besar dengan banyak switch, karena tidak perlu mengulang konfigurasi VLAN satu per satu di setiap switch.

##### Komponen

Domain VTP: semua switch yang menggunakan VTP harus berada dalam satu domain VTP yang sama, diidentifikasi dengan nama domain yang harus seragam di seluruh switch.

Mode operasi, ada tiga:

- **Server**: dapat membuat, mengubah, dan menghapus VLAN, serta mengirimkan advertisement VLAN ke switch lain secara berkala (biasanya setiap 5 menit); server juga menerima advertisement dari server lain dan memperbarui database VLAN jika ada perubahan dengan nomor revisi lebih tinggi. 
- **Client**: hanya menerima dan menyinkronkan informasi VLAN dari server. [
- **Transparent** : tidak mengirim maupun memproses advertisement, tapi tetap meneruskannya ke switch lain; VLAN yang dibuat bersifat lokal saja dan tidak memengaruhi switch lain.

Advertisement: Summary Advertisement berisi ringkasan database VLAN dan nomor revisi saat itu, ada juga Subset Advertisement dan Advertisement Request.

(Analoginya: bayangkan server itu seperti "pengumuman pusat" yang tiap beberapa menit teriak ke semua switch, "ini daftar VLAN terbaru versi ke-sekian!" — switch lain dengar itu, cek versinya lebih baru atau tidak, kalau lebih baru langsung update data VLAN-nya sendiri).

##### Konfigurasi

```
Switch(config)# vtp domain NAMA_DOMAIN
Switch(config)# vtp mode server      ! atau client / transparent
Switch(config)# vtp password PASSWORD
Switch(config)# exit
```

Cek dengan `do show vtp status`. Catatan dari artikel: semua switch yang akan saling bertukar informasi VLAN harus berada dalam satu domain VTP yang sama dan nama domain ini harus konsisten di seluruh switch.

##### Why?

###### Kenapa Domain Harus sama?

Domain VTP itu ibarat "nama grup" atau "identitas jaringan". VTP memakainya sebagai penanda: switch-switch mana saja yang boleh saling bertukar informasi VLAN.    

- Switch hanya akan **memproses dan mempercayai** advertisement yang datang dari domain yang sama dengan dirinya.
- Kalau nama domain beda, switch akan **mengabaikan** advertisement itu — dianggap bukan bagian dari jaringannya, meskipun secara fisik saling terhubung lewat trunk.

###### Kenapa Password harus Sama?

Password VTP berfungsi sebagai autentikasi. Setiap advertisement yang dikirim akan disertai semacam "tanda tangan" (MD5 hash) yang dihasilkan dari kombinasi isi advertisement + password.

- Switch penerima akan menghitung ulang hash tersebut menggunakan passwordnya sendiri.
- Kalau hasilnya cocok → advertisement dianggap valid dan diproses.
- Kalau password beda → hash tidak cocok → advertisement **ditolak**, walaupun domainnya sama.

###### kan vlan biar ruangan 1 dan 2 nga bisa sasling komunikasi tapi kalau subnetnya udah beda aja mereka nga bisa komunikasi

Celah keamanan. Ini yang paling krusial: kalau si PC di port yang "harusnya" subnet 10 itu dengan sengaja ganti IP-nya sendiri jadi 192.168.20.x, maka dia langsung bisa komunikasi ke subnet 20 tanpa lewat router sama sekali — karena secara fisik mereka memang satu segment Layer 2 yang sama. Pemisahan subnet doang itu sifatnya cuma "aturan main" di level IP, bukan pembatas fisik/hardware. Orang yang usil bisa dengan mudah bypass itu.

intinya :

192.168.20.x, misal berubah gitu dan nga pake vlan berarti ruangan 1 dan 2 bisa berhubungan tapi kalau pake vlan walau subnetnya sama tetap gabisa karena ada vlan yang membatasi.
