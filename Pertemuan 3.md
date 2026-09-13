# Inter - Vlan routing dan acl

### Intervlan Routing

##### Pengertian

Inter-VLAN routing adalah proses merutekan (routing) traffic antar VLAN yang berbeda. Secara default, VLAN yang berbeda **tidak bisa saling berkomunikasi** meskipun terhubung ke switch fisik yang sama — karena VLAN memisahkan broadcast domain di Layer 2 (MAC address).

Supaya perangkat di VLAN 10 bisa "ngobrol" dengan perangkat di VLAN 20, dibutuhkan perangkat yang bekerja di **Layer 3** (paham IP address) — yaitu router atau Layer 3 switch. Perangkat inilah yang menjembatani komunikasi antar VLAN.

Intinya: **VLAN memecah jaringan → Inter-VLAN routing menyambungkannya kembali secara terkontrol.**

##### Fungsi

- **Menghubungkan VLAN yang terpisah** — memungkinkan device di VLAN berbeda saling kirim data.
- **Tetap mempertahankan manfaat segmentasi VLAN** — broadcast domain tetap kecil dan terpisah, hanya traffic yang memang perlu saja yang dirutekan.
- **Kontrol akses antar VLAN** — karena traffic harus lewat Layer 3 (router/L3 switch), admin bisa pasang **Access Control List (ACL)** untuk membatasi siapa boleh akses VLAN mana.
- **Sentralisasi layanan** — satu server (DHCP, DNS, file server, dsb) bisa melayani banyak VLAN sekaligus tanpa perlu duplikasi.
- **Efisiensi penggunaan IP dan perangkat** — tidak perlu router/interface fisik terpisah untuk tiap VLAN.

##### Tujuan

- **Memungkinkan komunikasi lintas departemen/VLAN** yang memang dibutuhkan secara bisnis (misal akses ke server pusat, printer bersama, aplikasi internal).
- **Menjaga keseimbangan antara keamanan dan fungsionalitas** — VLAN mengamankan, inter-VLAN routing membuka jalur yang memang perlu dibuka saja (bisa dikombinasikan dengan ACL untuk membatasi lebih spesifik).
- **Mendukung skalabilitas jaringan** — jaringan enterprise besar dengan banyak divisi tetap bisa saling terhubung tanpa harus flat network (network besar tanpa segmentasi) yang rawan macet dan tidak aman.
- **Efisiensi infrastruktur** — satu physical link atau satu switch bisa menangani routing banyak VLAN sekaligus (terutama di metode router-on-a-stick).

#### Metode Inter-VLAN Routing

Ada dua pendekatan utama: **Router-on-a-Stick** dan **Layer 3 Switch (SVI)**. Pemilihan metode tergantung hardware yang tersedia — Layer 3 switch umumnya lebih cepat karena routing diproses di hardware (ASIC), sedangkan router-on-a-stick memproses routing di software sehingga lebih lambat dan rawan jadi bottleneck kalau traffic tinggi.

##### Router-on-a-Stick

Model di mana **satu interface fisik router** dihubungkan ke **satu port trunk** di switch, lalu interface fisik itu dipecah jadi beberapa **subinterface virtual** — satu subinterface untuk tiap VLAN. Router lalu merutekan traffic antar subinterface tersebut.

Disebut "on a stick" karena hanya ada **satu kabel/link** (satu "tongkat") yang membawa semua traffic VLAN menuju router

| Kelebihan | Kekurangan |
| --- | --- |
| Murah — cuma butuh 1 router & 1 kabel | Semua traffic antar VLAN numpuk di 1 link fisik → rawan jadi bottleneck |
| Mudah dikonfigurasi | Routing diproses di software (CPU router) → lebih lambat |
| Cocok untuk jaringan kecil/menengah | Kurang cocok untuk traffic tinggi / enterprise besar |

###### Cara Kerja

1. Frame dari VLAN manapun yang keluar dari switch menuju router akan **ditag** dengan nomor VLAN-nya (pakai 802.1Q) karena link-nya adalah trunk.
2. Router menerima frame yang sudah ditag itu di satu interface fisik, lalu subinterface yang sesuai dengan nomor tag itu yang memprosesnya.
3. Kalau traffic mau pindah dari VLAN 10 ke VLAN 20, router men-decapsulate tag VLAN 10, mengecek routing table, lalu mengirim ulang paket dengan tag VLAN 20 ke switch.
4. Switch menerima, membaca tag VLAN 20, dan meneruskan ke port yang sesuai.

###### Konfigurasi

Di Switch (siapkan trunk ke router):

```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

Di Router (bikin subinterface per VLAN):

```
Router(config)# interface fastEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface fastEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface fastEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit
```

**Catatan Penting:**

- `no shutdown` di interface fisik **wajib**, karena kalau interface fisik down, semua subinterface ikut down.
- Nomor di `encapsulation dot1Q <nomor>` **harus sama persis** dengan VLAN ID yang dikonfigurasi di switch, kalau tidak, tag tidak akan dikenali.
- IP address subinterface inilah yang jadi **default gateway** untuk PC di VLAN tersebut.

###### Analogi

**Router-on-a-stick** ibarat gedung yang cuma punya **satu lift** untuk menghubungkan semua lantai. Semua orang dari lantai manapun yang mau ke lantai lain harus lewat lift itu satu-satu — kalau orangnya banyak dan naik-turun terus, lift ini jadi antrean panjang (bottleneck).

#### Layer 3 Switch (Switch Virtual Interface / SVI)

Layer 3 switch adalah switch yang **selain bisa switching Layer 2, juga bisa routing Layer 3**. Alih-alih pakai router eksternal, switch ini punya interface logis yang disebut **SVI (Switch Virtual Interface)** — satu SVI per VLAN, dan SVI inilah yang berfungsi sebagai gateway untuk VLAN tersebut.

Tidak butuh kabel trunk menuju perangkat lain, karena routing terjadi **di dalam switch itu sendiri**.

| Kelebihan | Kekurangan |
| --- | --- |
| Jauh lebih cepat (routing di hardware/ASIC) | Perangkatnya lebih mahal dari switch Layer 2 biasa |
| Tidak ada single point of bottleneck seperti router-on-a-stick | Konfigurasi sedikit lebih kompleks (butuh paham routing + switching) |
| Cocok untuk jaringan besar/enterprise dengan traffic tinggi |     |

###### Cara Kerja

1. Switch tetap melakukan switching Layer 2 biasa untuk traffic dalam VLAN yang sama.
2. Kalau traffic ditujukan ke VLAN lain (device tahu ini dari default gateway-nya beda subnet), traffic dikirim ke SVI VLAN asal.
3. Switch, dengan `ip routing` aktif, mengecek routing table secara internal (diproses di hardware/ASIC — makanya cepat) dan meneruskan ke SVI VLAN tujuan.
4. Dari SVI tujuan, traffic di-switch secara Layer 2 ke port device tujuan di VLAN itu.

###### Konfigurasi

```
Layer3Switch(config)# interface vlan 10
Layer3Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Layer3Switch(config-if)# no shutdown
Layer3Switch(config-if)# exit

Layer3Switch(config)# interface vlan 20
Layer3Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Layer3Switch(config-if)# no shutdown

Layer3Switch(config)# ip routing
```

**Catatan Penting:**

- `ip routing` **wajib** diaktifkan, kalau tidak, switch cuma akan switching biasa dan SVI tidak akan merutekan apa pun walau IP sudah diset.
- `no shutdown` tetap wajib di tiap interface VLAN (SVI), defaultnya SVI dalam kondisi down/administratively down.

###### Analogi

**Layer 3 switch (SVI)** ibarat gedung yang punya **beberapa lift/tangga tersebar** di titik-titik strategis, jadi perpindahan antar lantai jauh lebih cepat dan tidak numpuk di satu titik saja.

#### Troubleshooting Umum

1. **VLAN mismatch** — VLAN ID di switch dan subinterface/SVI router tidak sama. Ini penyebab paling umum inter-VLAN routing gagal.
2. **Trunk belum diset dengan benar** — port yang menghubungkan switch ke router harus `switchport mode trunk`, bukan access.
3. **Lupa `no shutdown`** — baik di interface fisik router (router-on-a-stick) maupun SVI (Layer 3 switch), interface default-nya down.
4. **`ip routing` belum diaktifkan** di Layer 3 switch — SVI sudah dikonfigurasi IP tapi routing tetap tidak jalan kalau ini lupa.
5. **Default gateway PC salah** — pastikan IP gateway di PC sama persis dengan IP subinterface/SVI VLAN-nya.
6. **Cek dengan:**
  - `do show ip interface brief` → pastikan semua interface/subinterface/SVI status **up/up**.
  - `do show vlan brief` → pastikan port sudah masuk VLAN yang benar.
  - `do show interfaces trunk` → pastikan trunk aktif dan VLAN yang dibutuhkan diizinkan lewat.
  - `ping` dan `tracert`/`traceroute` dari PC untuk menelusuri di titik mana koneksi terputus.

#### WHY

1. Kenapa harus VLAN kalau pada akhirnya disambung (intervlan) lagi?

VLAN bukan mutusin total, tapi ngecilin broadcast domain dan bikin satu pintu terkontrol (bisa dipasang ACL). Tanpa VLAN, semua device satu broadcast domain besar tanpa checkpoint. Dengan VLAN, broadcast tetap kecil, komunikasi yang perlu tetap bisa lewat lewat gerbang yang diawasi.

2. Beda inter-VLAN routing vs routing biasa?

Konsepnya sama — sama-sama Layer 3 pakai routing table. Bedanya cuma pemisah network-nya: routing biasa beda kabel fisik, inter-VLAN routing beda tag VLAN walau lewat kabel yang sama. Makanya butuh dot1Q atau SVI buat baca tag itu.

3. Kenapa harus encapsulation dot1Q?

Karena link switch-router itu trunk, satu kabel bawa banyak VLAN sekaligus, tercampur. Dot1Q itu tag penanda "ini punya VLAN berapa". Tanpa itu, router nggak bisa bedain frame VLAN 10 dari VLAN 20 yang datang bareng.

4. Kenapa SVI harus ip routing?

Defaultnya Layer 3 switch cuma switching biasa, walau hardware-nya sanggup routing. `ip routing` itu saklar buat ngaktifin kemampuan Layer 3-nya. Kalau lupa, SVI tetap up/up dan ada IP, tapi switch nggak pernah cek routing table — jadi nggak bisa nyeberang VLAN walau kelihatannya semua udah bener.
