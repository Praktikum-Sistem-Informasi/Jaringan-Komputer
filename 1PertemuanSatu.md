# IP ADDRESS, TOPOLOGI  JARINGAN & SUBNETTING

### IP Address

IP Address (Internet Protocol Address) adalah alamat numerik unik yang diberikan kepada setiap perangkat yang terhubung ke dalam suatu jaringan komputer, baik jaringan lokal (LAN) maupun jaringan luas seperti internet.

- 32 bit dibagi menjadi 4 oktet (8 bit tiap oktet)

- Ditulis dalam desimal bertitik, misal 185.107.80.231

- Setiap oktet bernilai 0-255

- Terbagi atas Network ID dan Host ID

##### IP Public & IP Private

Private IP Address hanya digunakan dalam lingkup jaringan lokal (internal) dan tidak dapat diakses langsung dari internet. Public IP Address bersifat unik secara global dan dapat diakses langsung melalui internet.

```
Range IP Private

Class A: 10.0.0.0 - 10.255.255.255
Class B: 172.16.0.0 - 172.31.255.255
Class C: 192.168.0.0 - 192.168.255.255
```

```
Range IP Public
Class A: 1.0.0.0 - 9.255.255.255
                11.0.0.0 - 126.255.255.255


Class B: 128.0.0.0 - 172.15.255.255
                172.32.0.0 - 191.255.255.255


Class C: 192.0.0.0 - 192.167.255.255
                192.169.0.0 - 223.255.255.255
```

##### IP Static

Setiap kali perangkat terhubung ke jaringan, ia akan diberikan alamat IP yang sama setiap kali. Alamat IP statis sering digunakan untuk perangkat yang perlu diidentifikasi dengan konsistensi, seperti server web atau server email.

##### IP Dynamic

IP secara otomatis ke perangkat ketika mereka terhubung ke jaringan. Alamat IP dinamis sering digunakan untuk perangkat yang tidak memerlukan identifikasi yang konsisten, seperti komputer pribadi atau ponsel.

---

### Topologi Jaringan

Topologi jaringan komputer merupakan susunan fisik atau logis dari perangkat - perangkat jaringan, kabel dan konektor yang mengatur cara dimana perangkat - perangkat tersebut terhubung satu sama lain.

##### Topologi Star

Dalam topologi bintang, semua perangkat terhubung langsung ke pusat atau switch pusat. Keunggulannya jenis topologi ini mudah diatur, serta performa baik untuk jaringan kecil hingga menengah.

- **Contoh:** Jaringan kantor kecil-menengah, lab komputer sekolah, atau jaringan rumah dengan router sebagai pusat.
- **Kenapa cocok:** Mudah menambah/mengurangi perangkat tanpa mengganggu yang lain, dan kalau satu kabel/perangkat bermasalah, perangkat lain tetap jalan normal karena semua terhubung independen ke switch pusat.

##### Topologi Ring

Topologi ring menghubungkan setiap perangkat dalam siklus menyerupai lingkaran. Keunggulannya meliputi transmisi data satu arah tanpa bentrok dan kemudahan dalam mengidentifikasi masalah pada jaringan.

- **Contoh:** Jaringan MAN (Metropolitan Area Network) lama atau sistem token ring di beberapa jaringan industri/pabrik yang butuh transmisi data terjadwal dan bebas tabrakan data.
- **Kenapa cocok:** Karena data mengalir satu arah secara bergiliran, tidak ada tabrakan (collision) data, cocok untuk lingkungan yang butuh transmisi stabil dan mudah dilacak sumber gangguannya.

##### Topologi Bus

Dalam topologi bus, semua perangkat terhubung dalam satu jalur kabel tunggal yang disebut sebagai kabel backbone yang menjadi jalur pusat transmisi data. Topologi bus sangat mudah diterapkan dan hemat biaya untuk jaringan yang kecil.

- **Contoh:** Jaringan kecil sementara, seperti di warnet kecil zaman dulu atau lab percobaan sederhana dengan sedikit komputer.
- **Kenapa cocok:** Biaya kabel paling murah karena cuma butuh satu kabel utama (backbone), instalasi cepat—cocok kalau dananya terbatas dan jumlah perangkatnya sedikit.

##### Topologi Mesh

Dalam topologi mesh, setiap perangkat terhubung satu sama lain ke setiap perangkat lainnya yang terdapat dalam jaringan. Topologi ini sangat aman terhadap kegagalan teknis yang umumnya terjadi pada sebuah perangkat jaringan tertentu.

- **Contoh:** Jaringan militer, pusat data (data center), sistem perbankan, atau infrastruktur penerbangan yang butuh keandalan tinggi.
- **Kenapa cocok:** Karena tiap perangkat punya banyak jalur alternatif, kalau satu koneksi putus, data tetap bisa lewat jalur lain—sangat penting untuk sistem yang tidak boleh down.

##### Topologi Tree

Topologi tree adalah kombinasi antara beberapa topologi star dan topologi bus. Pusat topologi star terhubung ke banyak cabang jalur bus. Topologi jenis ini merupakan salah satu yang paling sering digunakan sebab mudah dikelola dan disesuaikan dengan kebutuhan.

- **Contoh:** Jaringan perusahaan besar dengan banyak cabang/departemen, seperti kampus universitas yang punya beberapa gedung atau kantor pusat dengan beberapa cabang kecil.
- **Kenapa cocok:** Menggabungkan kelebihan star (mudah dikelola per cabang) dan bus (efisien menghubungkan banyak grup), jadi cocok untuk jaringan skala besar yang butuh struktur hierarkis dan gampang dikembangkan.

---

### Subnetting

Subnetting adalah proses membagi satu blok alamat IP (network) menjadi beberapa jaringan yang lebih kecil (subnet), dengan meminjam sejumlah bit dari bagian Host ID untuk dijadikan bit tambahan pada Network ID.

##### Manfaat

- Efisiensi penggunaan alamat IP.

- Mempermudah manajemen jaringan (segmentasi).

- Meningkatkan keamanan (memisahkan segmen jaringan antar-divisi).

##### Komponen Subnetting

###### CIDR

Notasi singkat untuk subnet mask. Contoh: /27 mewakili panjang prefix jaringan.

###### Subnet Mask

Menentukan batas network dan host. Contoh: 255.255.255.224.

###### Network ID

Alamat pertama dalam subnet guna sebagai identitas jaringan, tidak bisa dipakai host.

###### Broadcast Address

Alamat terakhir dalam subnet yang digunakan untuk mengirim data ke semua host.

###### Host Range

Rentang alamat yang dapat digunakan untuk perangkat aktif dalam subnet

##### Rumus Subnetting

###### Total IPs

$$
2^{32 - CIDR}
$$

$$
2^{32 - 26} = 2^6 = 64
$$

###### Usable Host

$$
Total IP - 2
$$

$$
64 - 2 = 62
$$

###### Network Address

$$
⌊(Oktet ke-4 IP ÷ Total IP)⌋ × Total IP
$$

$$
120/64 = 1,875 > 1 x 64 = 64
$$

catatan : 

###### Broadcast Address

$$
NA + Total IP - 1
$$

$$
64 + 64 - 1 = 127
$$

###### Host Range

$$
Host Minimum = NA + 1
$$

$$
64 + 1 = 65
$$

$$
Host Maximum = BC - 1
$$

$$
127 - 1 = 126
$$

###### Subnet Mask

$$
256 - Total IP
$$

$$
256 - 64 = 192
$$

##### Contoh Subnetting Masing - Masing Kelas

###### Kelas A

```
Contoh: 10.0.0.0/16

Default Class A: /8 (255.0.0.0)
Range Oktet 1  : 1 - 126

IP         : 10.0.0.0/16

Total IP   : 2^(32-16) = 2^16 = 65.536 IP
Usable Host: 65.536 - 2 = 65.534 Host

Oktet yang kena  : oktet ke-2
Subnet Mask (SM) : 255.255.0.0

Network Address (NA)  : 10.0.0.0
Broadcast Address (BC): 10.0.255.255
Host Range            : 10.0.0.1 - 10.0.255.254
```

###### Kelas B

```
Contoh: 172.16.0.0/20

Default Class B: /16 (255.255.0.0)
Range Oktet 1  : 128 - 191

IP         : 172.16.0.0/20

Total IP   : 2^(32-20) = 2^12 = 4.096 IP
Usable Host: 4.096 - 2 = 4.094 Host

Oktet yang kena  : oktet ke-3
SM oktet ke-3    : 256 - 16 = 240
Subnet Mask (SM) : 255.255.240.0

Perhitungan NA:
0 ÷ 16 = 0 (floor) -> NA oktet ke-3 = 0 x 16 = 0

Network Address (NA)  : 172.16.0.0
Broadcast Address (BC): 172.16.15.255
Host Range            : 172.16.0.1 - 172.16.15.254
```

###### Kelas C

```
Contoh: 192.168.10.150/27
======================================
Default Class C: /24 (255.255.255.0)
Range Oktet 1  : 192 - 223

IP         : 192.168.10.150/27

Total IP   : 2^(32-27) = 2^5 = 32 IP
Usable Host: 32 - 2 = 30 Host

Oktet yang kena  : oktet ke-4
SM oktet ke-4    : 256 - 32 = 224
Subnet Mask (SM) : 255.255.255.224

Perhitungan NA:
150 ÷ 32 = 4,6875 (floor) -> 4
NA = 4 x 32 = 128

Network Address (NA)  : 192.168.10.128
Broadcast Address (BC): 192.168.10.159
Host Range            : 192.168.10.129 - 192.168.10.158
```
