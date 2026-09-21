# Dynamic Routing (EIGRP & OSPF)

### Topologi yang Digunakan

Topologi yang dipakai pada praktikum ini terdiri dari **3 router** (Router A, Router0, Router1) yang dihubungkan secara berurutan, dengan **5 network LAN (VLAN)** di sisi masing-masing router. Alamat IP interface router sudah terkonfigurasi pada file topologi, sehingga langkah praktikum langsung menuju konfigurasi routing.

### Dynamic Routing (Enhanced Interior Gateway Routing Protocol/EIGRP)

EIGRP adalah protokol routing dinamis buatan **Cisco** yang tergolong **advanced distance-vector (hybrid)**, karena menggabungkan sifat distance-vector dengan efisiensi link-state. EIGRP memakai algoritma **DUAL (Diffusing Update Algorithm)** untuk menentukan jalur terbaik (**successor**) sekaligus menyiapkan jalur cadangan (**feasible successor**), sehingga convergence saat terjadi perubahan topologi sangat cepat.

**Kapan cocok untuk dipakai?**

- Jaringan menengah sampai besar yang **seluruh perangkatnya Cisco**
- Jaringan yang butuh convergence sangat cepat dan jalur cadangan siap pakai
- Jaringan yang ingin memanfaatkan load balancing pada jalur dengan bandwidth berbeda

##### Karakteristik

- **Advanced distance-vector (hybrid)** - router berbagi informasi dengan tetangga seperti distance-vector, tetapi memakai tabel topologi dan DUAL seperti protokol yang lebih canggih
- **Metric komposit** - dihitung dari kombinasi bandwidth, delay, reliability, dan load (secara default hanya **bandwidth dan delay** yang dipakai)
- **Algoritma DUAL** - menghitung jalur terbaik (**successor**) dan jalur cadangan yang bebas loop (**feasible successor**)
- **Administrative Distance (AD)** = 90 (internal), 170 (external)
- **Update parsial & triggered** - hanya mengirim perubahan saat terjadi perubahan topologi, bukan seluruh tabel routing
- **Neighbor adjacency** - router saling mengenali tetangga lewat paket **Hello** (default tiap 5 detik) memakai multicast `224.0.0.10`
- **Harus satu Autonomous System (AS) number** - dua router hanya bisa bertetangga kalau nomor AS-nya sama
- **Mendukung VLSM & CIDR**
- **Mendukung unequal-cost load balancing** - bisa membagi trafik ke beberapa jalur walau metric-nya tidak sama (memakai `variance`)
- **Memiliki 3 tabel** - neighbor table, topology table, dan routing table

##### Kelebihan

- **Convergence sangat cepat** - Jalur cadangan (feasible successor) sudah siap sebelum jalur utama putus
- **Efisien bandwidth** - Hanya mengirim update parsial saat ada perubahan
- **Metric lebih akurat** - Mempertimbangkan bandwidth dan delay, bukan sekadar jumlah hop
- **Load balancing fleksibel** - Bisa membagi trafik ke jalur dengan kecepatan berbeda
- **Konfigurasi relatif mudah** - Lebih sederhana dibanding OSPF karena tidak ada konsep area
- **Bebas routing loop** - Dijamin oleh algoritma DUAL

##### Kekurangan

- **Awalnya proprietary Cisco** - Lebih optimal di perangkat Cisco; dukungan di vendor lain terbatas
- **Kurang cocok untuk jaringan multi-vendor** - Sulit dipakai jika ada perangkat non-Cisco
- **Tidak ada hierarki area** - Tidak sestruktur OSPF untuk jaringan yang sangat besar
- **Perlu AS number yang sama** - Kalau salah/berbeda, router tidak akan menjadi tetangga
- **Troubleshooting butuh pemahaman DUAL** - Misalnya kondisi *Stuck in Active (SIA)* pada jaringan besar

##### Konfigurasi

###### Router A

```
! Konfigurasi EIGRP (AS number 100)
Router(config)# router eigrp 100
Router(config-router)# network 192.168.10.0 0.0.0.15
Router(config-router)# network 192.168.20.0 0.0.0.15
Router(config-router)# network 10.10.10.0 0.0.0.3
Router(config-router)# no auto-summary
```

###### Router0

```
! Konfigurasi EIGRP (AS number 100)
Router(config)# router eigrp 100
Router(config-router)# network 10.10.10.0 0.0.0.3
Router(config-router)# network 192.168.30.0 0.0.0.15
Router(config-router)# network 192.168.40.0 0.0.0.15
Router(config-router)# network 20.20.20.0 0.0.0.3
Router(config-router)# no auto-summary
```

###### Router1

```
! Konfigurasi EIGRP (AS number 100)
Router(config)# router eigrp 100
Router(config-router)# network 192.168.50.0 0.0.0.15
Router(config-router)# network 20.20.20.0 0.0.0.3
Router(config-router)# no auto-summary
```

> AS number pada EIGRP adalah identitas domain routing, dan dua router hanya bisa menjadi neighbor jika angkanya sama. Angkanya bebas dipilih (1-65535) tanpa perlu izin siapa pun, dan pemakaian 1, 10, atau 100 hanyalah kebiasaan, bukan aturan teknis.

### Dynamic Routing (Open Shortest Path First/OSPF)

OSPF adalah protokol routing dinamis berbasis **link-state** dan merupakan **standar terbuka (open standard, RFC 2328)**, sehingga bisa dipakai lintas vendor perangkat jaringan. Setiap router OSPF saling bertukar informasi **LSA (Link State Advertisement)** untuk membentuk **LSDB (Link State Database)** yang sama di seluruh router dalam satu area, lalu menghitung jalur terpendek secara mandiri menggunakan **algoritma Dijkstra (Shortest Path First)**.

**Kapan cocok untuk dipakai?**

- Jaringan menengah sampai besar (enterprise, kampus, ISP)
- Jaringan yang memakai perangkat dari berbagai vendor (bukan hanya Cisco)
- Jaringan yang butuh convergence cepat dan bisa dibagi menjadi beberapa area agar lebih terstruktur

##### Karakteristik

- **Link-state protocol** - setiap router tahu topologi jaringan secara utuh (lewat LSDB), bukan hanya arah dan jarak seperti pada RIP
- **Metric = cost** - dihitung dari bandwidth interface (`reference bandwidth / bandwidth interface`); makin besar bandwidth, makin kecil cost, makin diutamakan jalurnya
- **Tidak ada batas hop** - tidak dibatasi 15 hop seperti RIP
- **Administrative Distance (AD)** = 110
- **Hierarki area** - jaringan dibagi ke dalam beberapa area, dengan **Area 0 (backbone)** sebagai pusat yang menghubungkan area lain
- **Update hanya saat ada perubahan (triggered)** - bukan mengirim seluruh tabel routing secara periodik seperti RIP
- **Neighbor adjacency** - router saling mengenali tetangga lewat paket **Hello** (default tiap 10 detik) dan menggunakan multicast `224.0.0.5` serta `224.0.0.6` (untuk DR/BDR)
- **Mendukung VLSM & CIDR** - subnet mask ikut dikirim dalam update, sehingga fleksibel untuk pembagian subnet
- **Memakai wildcard mask** saat mendaftarkan network (bukan subnet mask)

##### Kelebihan

- **Convergence cepat** - Perubahan topologi langsung diinformasikan dan dihitung ulang (SPF)
- **Skalabel** - Cocok untuk jaringan besar karena bisa dibagi menjadi beberapa area
- **Standar terbuka** - Didukung berbagai vendor (Cisco, Mikrotik, Juniper, Huawei, dll)
- **Efisien bandwidth** - Hanya mengirim update saat ada perubahan, bukan seluruh tabel secara periodik
- **Bebas routing loop** - Karena setiap router punya gambaran topologi yang sama (LSDB)
- **Mempertimbangkan bandwidth** - Pemilihan jalur berdasarkan cost, bukan sekadar jumlah hop

##### Kekurangan

- **Konfigurasi lebih rumit** - Dibanding RIP, ada konsep area, wildcard mask, dan DR/BDR yang harus dipahami
- **Butuh resource lebih besar** - CPU dan memori terpakai untuk menyimpan LSDB dan menjalankan perhitungan SPF
- **Perlu perencanaan desain area** - Desain area yang kurang baik bisa membuat jaringan sulit dikelola
- **Troubleshooting lebih kompleks** - Terutama pada jaringan multi-area

##### Konfigurasi

###### Router A

```
! Konfigurasi OSPF (process-id 10, semua network masuk area 0)
Router(config)# router ospf 10
Router(config-router)# network 192.168.10.0 0.0.0.15 area 0
Router(config-router)# network 192.168.20.0 0.0.0.15 area 0
Router(config-router)# network 10.10.10.0 0.0.0.3 area 0
```

###### Router0

```
! Konfigurasi OSPF (process-id 10, semua network masuk area 0)
Router(config)# router ospf 10
Router(config-router)# network 10.10.10.0 0.0.0.3 area 0
Router(config-router)# network 192.168.30.0 0.0.0.15 area 0
Router(config-router)# network 192.168.40.0 0.0.0.15 area 0
Router(config-router)# network 20.20.20.0 0.0.0.3 area 0
```

###### Router1

```
! Konfigurasi OSPF (process-id 10, semua network masuk area 0)
Router(config)# router ospf 10
Router(config-router)# network 192.168.50.0 0.0.0.15 area 0
Router(config-router)# network 20.20.20.0 0.0.0.3 area 0
```

> Process ID pada OSPF hanyalah label lokal untuk membedakan beberapa proses OSPF di satu router. Angka ini tidak dikirim di paket OSPF, sehingga tidak perlu sama antar-router. Yang harus cocok adalah area ID, subnet, timer, dan autentikasi. Ini berbeda dengan AS number EIGRP yang ada di header paket dan wajib sama.

### Perbandingan RIP, OSPF, dan EIGRP

| Aspek                   | RIP                                | OSPF                                  | EIGRP                                  |
| ----------------------- | ---------------------------------- | ------------------------------------- | -------------------------------------- |
| Jenis protokol          | Distance-vector                    | Link-state                            | Advanced distance-vector (hybrid)      |
| Metric                  | Hop count                          | Cost (berdasarkan bandwidth)          | Komposit (bandwidth & delay)           |
| Administrative Distance | 120                                | 110                                   | 90 (internal)                          |
| Batas jaringan          | Maksimal 15 hop                    | Tidak ada batas hop                   | Default 100 hop (bisa diatur)          |
| Algoritma               | Bellman-Ford                       | Dijkstra (SPF)                        | DUAL                                   |
| Update                  | Periodik (30 detik), seluruh tabel | Triggered, saat ada perubahan         | Triggered, update parsial              |
| Kecepatan convergence   | Lambat                             | Cepat                                 | Sangat cepat                           |
| Standar                 | Terbuka                            | Terbuka (RFC 2328)                    | Awalnya proprietary Cisco              |
| Cocok untuk             | Jaringan kecil / lab               | Jaringan menengah-besar, multi-vendor | Jaringan menengah-besar berbasis Cisco |

### Istilah

###### Wildcard Mask

Kebalikan dari subnet mask yang dipakai saat mendaftarkan network di OSPF/EIGRP; bit `0` artinya harus cocok, bit `1` artinya tidak diperhatikan (contoh: subnet mask `255.255.255.0` menjadi wildcard `0.0.0.255`)

###### Autonomous System (AS) Number

Nomor yang mengelompokkan router dalam satu domain routing EIGRP; router hanya akan bertetangga jika AS number-nya sama

###### DUAL (Diffusing Update Algorithm)

Algoritma EIGRP untuk menghitung jalur terbaik yang dijamin bebas routing loop

###### Successor

Jalur utama (terbaik) menuju suatu network yang dipilih EIGRP dan dimasukkan ke tabel routing

###### Feasible Successor

Jalur cadangan yang sudah disiapkan EIGRP dan bebas loop, sehingga bisa langsung menggantikan successor tanpa perhitungan ulang jika jalur utama putus

###### Neighbor / Adjacency

Hubungan antar router tetangga yang sudah saling bertukar paket Hello dan siap bertukar informasi routing

###### Topology Table

Tabel EIGRP yang menyimpan semua jalur yang diketahui menuju suatu network, termasuk successor dan feasible successor

###### Link-State

Jenis protokol routing di mana setiap router memiliki gambaran topologi jaringan secara utuh, lalu menghitung sendiri jalur terbaiknya; dipakai oleh OSPF

###### LSA (Link State Advertisement)

Paket informasi berisi keadaan link/jaringan yang dikirim router OSPF ke router lain untuk membentuk database yang sama

###### LSDB (Link State Database)

Database yang menyimpan seluruh LSA; dipakai router OSPF sebagai bahan perhitungan jalur terpendek dengan algoritma Dijkstra

###### Area

Pembagian jaringan OSPF menjadi kelompok-kelompok agar lebih terstruktur; **Area 0** adalah backbone yang harus menjadi pusat penghubung area lain

###### Cost

Metric pada OSPF yang dihitung dari bandwidth interface; makin kecil cost, makin diutamakan jalurnya

###### Router ID

Identitas unik router dalam domain OSPF (berformat seperti IP address); jika tidak diatur manual, dipilih dari IP loopback tertinggi atau IP interface aktif tertinggi

###### DR / BDR (Designated Router / Backup Designated Router)

Router yang dipilih pada jaringan multi-access (misalnya Ethernet) untuk menjadi pusat pertukaran informasi OSPF, agar tidak semua router saling bertukar update satu sama lain
