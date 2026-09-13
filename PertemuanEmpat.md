# Static & Dynamic Routing (RIP)

### Routing

##### Pengertian

Routing adalah suatu proses dalam jaringan yang meneruskan paket data dari satu jaringan ke jaringan lainnya melalui internet. Dengan kata lain, routing adalah teknik yang memungkinkan data dikirim melalui jalur terbaik menuju tujuan yang diinginkan.

Dalam jaringan komputer, perangkat yang berperan dalam melakukan routing adalah router. Router bertugas menerima paket data yang ditujukan ke jaringan lain, kemudian meneruskannya ke *router* berikutnya hingga data mencapai tujuan akhirnya.

Routing bekerja dengan cara meneruskan paket data dari satu perangkat ke perangkat lainnya melalui jaringan. Proses ini melibatkan beberapa langkah berikut:

1. **Pengiriman Data**: Perangkat pengirim mengirimkan data dalam bentuk paket.
2. **Identifikasi Tujuan**: Router membaca *alamat IP* tujuan dari paket data tersebut.
3. **Pemilihan Jalur**: Router memilih jalur terbaik berdasarkan tabel routing.
4. **Pengiriman Paket**: Router meneruskan paket ke router berikutnya hingga sampai ke tujuan akhir.

###### Routing Vs Inter-Vlan routing

**Routing** adalah proses meneruskan paket data antar network yang berbeda menggunakan router, baik dalam skala lokal, antar kantor, maupun ke internet. **Inter-VLAN routing** adalah proses meneruskan paket data antar VLAN yang berbeda dalam satu jaringan lokal, agar perangkat di VLAN yang berbeda dapat saling berkomunikasi.

**Kesimpulannya** inter-VLAN routing merupakan bagian dari routing, tetapi tidak semua routing termasuk inter-VLAN routing, karena inter-VLAN routing adalah penerapan konsep routing secara khusus untuk menghubungkan segmen-segmen VLAN dalam satu jaringan lokal.

| Aspek        | Routing                              | Inter-VLAN Routing                                 |
| ------------ | ------------------------------------ | -------------------------------------------------- |
| Cakupan      | Bisa antar LAN, WAN, atau internet   | Khusus antar VLAN dalam satu jaringan lokal        |
| Perangkat    | Router (bisa multi-lokasi)           | Router-on-a-stick atau Layer 3 switch              |
| Contoh kasus | Kantor A ke Kantor B, akses internet | VLAN Finance ke VLAN Marketing di gedung yang sama |

##### Fungsi

Routing memiliki beberapa fungsi utama dalam jaringan, antara lain:

- **Menentukan Jalur Terbaik**: Router memilih jalur terbaik untuk mengirim data berdasarkan berbagai faktor seperti kecepatan dan kemacetan jaringan.

- **Menghubungkan Jaringan Berbeda**: Routing memungkinkan komunikasi antara jaringan yang berbeda, termasuk LAN, WAN, dan internet.

- **Mengelola Lalu Lintas Data**: Routing membantu mengatur lalu lintas data sehingga tidak terjadi kemacetan dalam jaringan.

### Static Routing

Static routing adalah metode routing di mana administrator jaringann mengonfigurasi rute (jalur) secara manual ke router, bukan router yang menentukan sendiri secara otomatis seperti pada routing dinamis. 

**Kapan cocok untuk dipakai?**

- Jaringan kecil dengan sedikit network/router
- Jaringan yang topologinya jarang berubah
- Sebagai **default route** menuju internet (paling umum dipakai di jaringan kecil-menengah)
- Sebagai **backup route** (floating static route) kalau rute dinamis gagal

##### Karakteristik

- **Manual** - semua rute diinput satu per satu oleh admin
- **Tidak otomatis update** - kalau ada perubahan topologi jaringan (link putus, network baru), admin harus update rute secara manual
- **Tidak memakai protokol routing** (tidak seperti OSPF, EIGRP, BGP)
- **Administrative Distance (AD)** = 1 (lebih dipercaya/prioritas dibanding rute dinamis, kecuali connected route yang AD-nya 0)

##### Kelebihan

- **Sederhana** - Tidak perlu konfigurasi protokol yang rumit

- **Aman** - Tidak ada pertukaran informasi routing antar router, jadi lebih sulit disusupi

- **Hemat resource** - Tidak membebani CPU/bandwidth router karena tidak ada proses perhitungan rute otomatis

- **Predictable** - Jalur paket bisa dipastikan/dikontrol penuh oleh admin

##### Kekurangan

- **Tidak scalable** - Merepotkan kalau jaringan besar dengan banyak network

- **Rentan human error** - Salah ketik IP/subnet bisa bikin masalah

- **Tidak fleksibel** -  Kalau ada link mati, tidak otomatis mencari jalur alternatif (kecuali dikonfigurasi floating static route)

- **Butuh maintenance manual** - Setiap ada perubahan topologi, admin harus update manual

##### Konfigurasi

![](C:/Users/Sadikin/AppData/Roaming/marktext/images/2026-09-13-18-58-04-image.png)

###### Router 1

```
Router1> enable
Router1# configure terminal

! Konfigurasi interface ke jaringan lokal (192.168.10.0)
Router1(config)# interface fastethernet 0/0
Router1(config-if)# ip address 192.168.10.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

! Konfigurasi interface ke Router2 (1.1.1.0)
Router1(config)# interface serial 0/0/0
Router1(config-if)# ip address 1.1.1.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

! Static routing menuju jaringan 192.168.20.0 
Router1(config)# ip route 192.168.20.0 255.255.255.0 1.1.1.2
```

###### Router 2

```
Router2> enable
Router2# configure terminal

! Konfigurasi interface ke jaringan lokal (192.168.20.0)
Router2(config)# interface fastethernet 0/0
Router2(config-if)# ip address 192.168.20.1 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

! Konfigurasi interface ke Router1 (1.1.1.0)
Router2(config)# interface serial 0/0/0
Router2(config-if)# ip address 1.1.1.2 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

! Static routing menuju jaringan 192.168.10.
Router2(config)# ip route 192.168.10.0 255.255.255.0 1.1.1.1
```

### Dynamic Routing (Routing Information Protocol/RIP)

RIP adalah salah satu protokol routing dinamis tertua dan paling sederhana, yang menggunakan **jumlah hop (hop count)** sebagai metode untuk menentukan jalur terbaik menuju suatu network.

**Kapan cocok untuk dipakai?**

- Jaringan kecil dengan topologi sederhana
- Untuk keperluan belajar/lab dasar routing dinamis
- Tidak disarankan untuk jaringan enterprise/besar — biasanya diganti dengan OSPF atau EIGRP

##### Karakteristik

- **Distance-vector protocol** - router hanya tahu arah dan jarak (jumlah hop) ke tujuan, tidak tahu topologi jaringan secara keseluruhan
- **Metric = hop count** - setiap kali paket melewati satu router (hop), nilainya bertambah 1
- **Maksimal 15 hop**  -  kalau lebih dari 15 hop, network dianggap **unreachable** (infinity)
- **Administrative Distance (AD)** = 120
- Update rute dikirim secara **broadcast/multicast** ke semua router tetangga secara periodik (biasanya tiap 30 detik)

##### Kelebihan

- **Sederhana** - Mudah dikonfigurasi, cocok untuk pemula
- **Kompatibilitas luas** - Didukung hampir semua perangkat router
- **Konfigurasi minim** - Tidak perlu banyak parameter tambahan

##### Kekurangan

- **Terbatas 15 hop** - Tidak cocok untuk jaringan besar
- **Konvergensi lambat** - Perlu waktu lebih lama dibanding OSPF/EIGRP untuk update ke seluruh jaringan
- **Boros bandwidth** - Mengirim seluruh tabel routing tiap update, bukan cuma perubahan
- **Rentan routing loop** - Meski ada mekanisme pencegahan (split horizon, poison reverse), tetap kurang efisien dibanding link-state protocol
- **Tidak mempertimbangkan bandwidth/kecepatan link** - Hanya berdasarkan jumlah hop, jadi bisa memilih jalur yang sebenarnya lebih lambat tapi hop-nya lebih sedikit

##### Konfigurasi

![](C:/Users/Sadikin/AppData/Roaming/marktext/images/2026-09-13-18-58-17-image.png)

###### Router 1

```
Router1> enable
Router1# configure terminal

! Konfigurasi interface ke jaringan lokal (192.168.10.0)
Router1(config)# interface fastethernet 0/0
Router1(config-if)# ip address 192.168.10.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

! Konfigurasi interface ke Router2 (1.1.1.0)
Router1(config)# interface serial 0/0/0
Router1(config-if)# ip address 1.1.1.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

! Konfigurasi RIP
Router1(config)# router rip
Router1(config-router)# network 192.168.10.0
Router1(config-router)# network 1.1.1.0
```

###### Router 2

```
Router2> enable
Router2# configure terminal

! Konfigurasi interface ke jaringan lokal (192.168.20.0)
Router2(config)# interface fastethernet 0/0
Router2(config-if)# ip address 192.168.20.1 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

! Konfigurasi interface ke Router1 (1.1.1.0)
Router2(config)# interface serial 0/0/0
Router2(config-if)# ip address 1.1.1.2 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

! Konfigurasi interface ke Router3 (2.2.2.0)
Router2(config)# interface serial 0/0/1
Router2(config-if)# ip address 2.2.2.1 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

! Konfigurasi RIP
Router2(config)# router rip
Router2(config-router)# network 192.168.20.0
Router2(config-router)# network 1.1.1.0
Router2(config-router)# network 2.2.2.0
```

###### Router 3

```
Router3> enable
Router3# configure terminal

! Konfigurasi interface ke jaringan lokal (192.168.30.0)
Router3(config)# interface fastethernet 0/0
Router3(config-if)# ip address 192.168.30.1 255.255.255.0
Router3(config-if)# no shutdown
Router3(config-if)# exit

! Konfigurasi interface ke Router2 (2.2.2.0)
Router3(config)# interface serial 0/0/0
Router3(config-if)# ip address 2.2.2.2 255.255.255.0
Router3(config-if)# no shutdown
Router3(config-if)# exit

! Konfigurasi RIP
Router3(config)# router rip
Router3(config-router)# network 192.168.30.0
Router3(config-router)# network 2.2.2.0
```



### Istilah

###### Administrative Distance (AD)

Angka yang menunjukkan seberapa "dipercaya" suatu rute; makin kecil angkanya, makin diutamakan

###### Hop / Hop count

Setiap kali data melewati satu router dihitung sebagai 1 "hop"; dipakai RIP untuk menentukan jalur terpendek

###### Konvergensi (convergence)

Kondisi ketika semua router sudah punya informasi rute yang sama dan sinkron
