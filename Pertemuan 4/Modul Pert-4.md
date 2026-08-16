# Pertemuan 4: Static Routing & Dynamic Routing (RIP)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar routing serta perbedaan mendasar antara static routing dan dynamic routing pada jaringan komputer.
- Praktikan mampu melakukan konfigurasi dasar dengan mengalamatkan IP pada interface perangkat Router Cisco.
- Praktikan mampu mengonfigurasi static route secara manual untuk menghubungkan antar network yang berbeda pada topologi multi router.
- Praktikan mampu memahami karakteristik dan mekanisme kerja protokol routing dinamis RIP (Routing Information Protocol).
- Praktikan mampu mengonfigurasi dynamic routing menggunakan RIP untuk melakukan pertukaran informasi routing secara otomatis antar router.
- Praktikan mampu melakukan verifikasi dan troubleshooting tabel routing menggunakan perintah show ip route pada CLI Cisco.


## 📁 Struktur Folder
```
.
├── soal/       # Soal atau instruksi tugas
└── docs/       # Materi pendukung (slide, referensi)
```
- `soal/` — berisi skenario tugas: ?.
- `docs/` — berisi modul ini beserta materi pendukung lain(?).

## 🚀 Cara Menjalankan
Praktikum ini menggunakan **Cisco Packet Tracer**, bukan bahasa pemrograman. Alur pengerjaannya:
```
# 1. Buka file topologi pada Cisco Packet Tracer, pastikan untuk mematikan internet
# 2. Klik perangkat Router, buka tab CLI, lalu masukkan perintah konfigurasi, contoh:
Router> enable
Router# configure terminal
Router(config)#interface FastEthernet0/1
Router(config-if)#no shutdown (Jalankan perintah `no shutdown` pada setiap port router yang tersedia agar seluruh port dapat berfungsi.)
```
## 📖 Materi Praktikum

### 1. Routing
Routing adalah teknologi pada router untuk menentukan jalur terbaik pengiriman data antar jaringan, yang sangat mempengaruhi konektivitas dan kecepatan transmisi data.

Terdapat dua jenis routing: static routing, yaitu konfigurasi jalur transmisi data secara manual oleh administrator, cocok untuk jaringan kecil karena stabil dan aman namun kurang fleksibel untuk jaringan skala menengah-besar; dan dynamic routing, yaitu metode di mana router saling bertukar informasi jalur secara otomatis menggunakan protokol tertentu berdasarkan metrik seperti kecepatan atau jarak, sehingga lebih fleksibel dan cocok untuk jaringan skala menengah-besar.

Sedangkan dynamic routing adalah metode
pengaturan jalur jaringan di mana perangkat jaringan, seperti router, menggunakan
protokol khusus untuk berkomunikasi satu sama lain dan secara otomatis memperbarui
tabel routing mereka. Protokol ini memungkinkan perangkat-perangkat tersebut berbagi
informasi tentang jaringan dan memutuskan jalur terbaik untuk mengirimkan data
berdasarkan metrik tertentu, seperti kecepatan atau jarak. Dynamic routing memiliki
keunggulan pada fleksibilitas, dinamika update penentuan jalur transmisi dan kemudahan
implementasi pada jaringan berskala menengah hingga besar.

### 2. Konfigurasi dan Penjelasan Static Routing

Static routing merupakan sebuah fitur untuk menetapkan jalur transmisi data dalam jaringan komputer secara manual dengan mendaftarkan setiap jaringan yang ada pada 
daftar routing dalam sebuah perangkat router. Jadi dapat disimpulkan bahwa konfigurasi routing hanya dikonfigurasikan pada perangkat jaringan yang memiliki kemampuan untuk
menjalankan fungsi routing seperti router dan switch layer 3. Static routing perlu dilakukan ketika jaringan yang ditujukan saat berkomunikasi berjarak lebih dari 1 hop dari jaringan yang menginisiasi transmisi data.

## Cheatsheet CLI Konfigurasi Static Routing

| Fungsi                         | Perintah CLI                                                                 | Penjelasan                                                                                             |
|--------------------------------|------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| Menambah static route          | `Router(config)# ip route (Network Address destination) (Subnetmask destination) (Gateway dari router untuk menuju destination network)` | Mendaftarkan jalur manual menuju network tujuan lewat gateway atau interface tertentu.                 |
| Menambah floating static route | `Router(config)# ip route(Network Address destination) (Subnetmask destination) (Gateway dari router untuk menuju destination network) (AD)`         | Menambahkan route cadangan dengan Administrative Distance lebih besar, hanya aktif kalau route utama hilang. |
| Menghapus static route         | `Router(config)# no ip route (Network Address destination) (Subnetmask destination) (Gateway dari router untuk menuju destination network)`           | Menghapus entri static route yang sudah dibuat.                                                        |
| Melihat tabel routing          | `Router# show ip route`                                                           | Menampilkan seluruh route aktif, termasuk static route (kode S).                                       |
| Melihat static route saja      | `Router# show ip route static`                                                    | Menampilkan hanya entri route yang berasal dari konfigurasi static.                                    |

#### Langkah kerja:  
<img width="312" height="295" alt="Screenshot 2026-08-17 014222" src="https://github.com/user-attachments/assets/63ca75b5-0e35-43c1-afda-3dc064e3fe8c" />  

Pada topologi di atas, terdapat dua buah router yang menghubungkan tiga buah
jaringan. Administrator perlu melakukan konfigurasi routing pada jaringan yang berjarak
lebih dari 1 hop dari router atau gateway jaringan sumber dan mendaftarkannya pada setiap
router yang ada. Pada konteks topologi di atas, Anda perlu melakukan routing pada Router
1 menuju jaringan 192.168.20.0 (Biru) dan pada Router 2 menuju jaringan 192.168.10.0
(Merah). Untuk bisa melakukan konfigurasi, pastika Anda telah melakukan konfigurasi
pada setiap PC dan interface router yang digunakan.

Sebelum melakukan konfigurasi routing, Anda bisa melihat daftar alamat jaringan
yang telah terkoneksi pada router menggunakan perintah berikut.
```
#do show ip route
> Menampilkan daftar alamat jaringan yang telah terdaftar pada router
```
R1  
<img width="429" height="167" alt="Screenshot 2026-08-17 015655" src="https://github.com/user-attachments/assets/5f2b95bb-d1b8-45d6-98ce-a9a256fe7c5e" />  
R2  
<img width="403" height="169" alt="Screenshot 2026-08-17 015734" src="https://github.com/user-attachments/assets/0708ee45-83e7-4e1d-88d8-b70ec47feed1" />  

Dapat dilihat pada gambar di atas hanya jaringan yang terkoneksi secara langsung ke
router yang terdaftar pada daftar routing. Hal tersebut menyebabkan PC 1 hanya bisa
melakukan komunikasi dengan PC 2 saja karena masih berada pada jaringan yang sama,
namun tidak bisa melakukan komunikasi dengan PC 3 dan PC 4 yang terdaftar pada
jaringan berbeda (biru) dan berjarak 2 hop dari router gateway jaringan merah.  

<img width="294" height="260" alt="Screenshot 2026-08-17 021303" src="https://github.com/user-attachments/assets/82d522be-3a58-4851-9463-accb78cecc38" />  
 
Agar PC 1 dan PC 2 pada jaringan merah bisa terkoneksi dengan PC 3 dan PC 4 pada
jaringan biru maka Anda perlu melakukan routing pada kedua router yang
menghubungkan jaringan-jaringan tersebut dengan konfigurasi seperti berikut.

```
#ip route (Network Address destination) (Subnetmask destination) (Gateway dari router untuk menuju destination network)
> Konfigurasi static routing
```

R1  
<img width="401" height="175" alt="Screenshot 2026-08-17 022646" src="https://github.com/user-attachments/assets/325321e6-64fd-4759-8275-80f4492e6665" />  
R2  
<img width="402" height="176" alt="Screenshot 2026-08-17 022734" src="https://github.com/user-attachments/assets/ad2f8fb9-67ff-4cea-b134-be0efba2e778" />  

Dapat dilihat pada daftar tabel routing yang terdapat pada Router 1 dan Router 2 di
atas, setelah dilakukan konfigurasi routing maka akan terdapat jaringan tujuan yang telah
dirouting secara manual ditandai dengan kode “S” (Static) pada kolom tipe routing yang
diterapkan. Setelah router mengetahui jalur transmisi data yang akan digunakan untuk
menuju jaringan tertentu yang lebih dari 2 hop, maka komputer yang ada di jaringan-
jaringan tersebut telah terkoneksi satu sama lain.

PC Ruangan Merah  
<img width="297" height="137" alt="Screenshot 2026-08-17 022958" src="https://github.com/user-attachments/assets/c4f90fb6-469a-43cc-b3ba-ea3208bf4df2" />  

Pc Ruangan Biru  
<img width="317" height="154" alt="Screenshot 2026-08-17 023155" src="https://github.com/user-attachments/assets/54d5565a-8347-4af2-8d8d-ac3a98ad2e82" />  

### 3. Konfigurasi dan Penjelasan Dynamic Routing (RIP)

Dynamic routing memungkinkan router saling bertukar informasi jalur secara otomatis untuk menentukan rute terbaik. RIP (Routing Information Protocol) adalah salah satu protokol dynamic routing paling sederhana, termasuk kategori interior routing protocol untuk jaringan lokal.

RIP bekerja dengan menukar informasi antar-router tetangga dan menentukan jalur terbaik berdasarkan hop count (jumlah router yang dilewati) jadi semakin sedikit hop, semakin baik. Update tabel routing dikirim secara periodik tiap 30 detik. Kelemahan utamanya adalah batas maksimum 15 hop, jadi kalau tujuannya lebih jauh dari itu, RIP tidak bisa menjangkau.
## Cheatsheet CLI Konfigurasi Dynamic Routing RIP

| Fungsi                          | Perintah CLI                                      | Penjelasan                                                                                           |
|---------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Masuk mode konfigurasi RIP      | `Router(config)# router rip`                      | Mengaktifkan proses routing RIP dan masuk ke mode konfigurasinya.                                    |
| Menentukan versi RIP            | `Router(config-router)# version 2`                | Mengaktifkan RIPv2 (mendukung VLSM dan tidak mengirim update broadcast, beda dari RIPv1).            |
| Mendaftarkan network            | `Router(config-router)# network [network_id]`     | Mendaftarkan network yang terhubung langsung agar ikut diiklankan lewat RIP.                         |
| Menonaktifkan auto-summary      | `Router(config-router)# no auto-summary`          | Mencegah RIP meringkas network ke classful boundary, penting saat pakai subnetting/VLSM.             |
| Menghapus proses RIP            | `Router(config)# no router rip`                   | Menonaktifkan seluruh proses RIP pada router.                                                        |
| Melihat tabel routing           | `Router# show ip route`                           | Menampilkan seluruh route aktif, termasuk route hasil RIP (kode R).                                  |
| Melihat status protokol         | `Router# show ip protocols`                       | Menampilkan detail proses routing aktif: versi, network yang didaftarkan, timer update, dan sumber route. |
| Melihat tetangga RIP            | `Router# show ip rip database`                    | Menampilkan seluruh route yang tersimpan di database RIP, termasuk yang belum tentu masuk tabel routing utama. |
| Debug update RIP (real-time)    | `Router# debug ip rip`                            | Menampilkan proses pengiriman dan penerimaan update RIP secara langsung, berguna untuk melihat bahwa RIP tetap "hidup" di background. |  

#### Langkah kerja:  
<img width="462" height="262" alt="Screenshot 2026-08-17 030547" src="https://github.com/user-attachments/assets/164584a9-c68e-4ddb-8f9d-b50ba44c348d" />  

Pada topologi di atas, terdapat tiga buah perangkat router yang terkoneksi satu sama
lain. Ketika Anda melakukan konfigurasi RIP pada ketiga Router yang ada, maka setiap
router akan mengirimkan informasi berupa alamat jaringan yang terhubung dan terdaftar
pada tabel routing mereka ke router yang berada di sebelahnya dan begitu pula sebaliknya.

```
#do show ip route
> Menampilkan daftar routing pada setiap router
```
R1  
<img width="401" height="172" alt="Screenshot 2026-08-17 030642" src="https://github.com/user-attachments/assets/fc07d5c8-c1f6-49fb-84b5-d8665574d762" />  
R2  
<img width="428" height="194" alt="Screenshot 2026-08-17 030719" src="https://github.com/user-attachments/assets/6ce49818-8f53-4331-a1a6-f863ec89d689" />   
R3  
<img width="406" height="169" alt="Screenshot 2026-08-17 030803" src="https://github.com/user-attachments/assets/8bea67f6-5ff7-4ae7-bfb5-6e2a59c789e5" />

Dapat dilihat pada gambar di atas, sebelum dilakukan konfigurasi RIP pada ketiga
router hanya mendeteksi jaringan yang terhubung secara langsung (default) pada mereka
saja, namun belum mengetuhi jaringan yang terhubung pada router lain. Selanjutnya Anda
bisa melakukan konfigurasi RIP pada setiap rouet dengan konfigurasi berikut.

```
#router rip (NA jaringan yangg terhubung langsung pada
router)
> Konfigurasi routing dengan protokol RIP
```
R1  
<img width="222" height="80" alt="Screenshot 2026-08-17 031453" src="https://github.com/user-attachments/assets/05419ac9-ef55-445b-b20c-993b2d5ce063" />  
R2  
<img width="227" height="80" alt="Screenshot 2026-08-17 031508" src="https://github.com/user-attachments/assets/42093f7f-c9c2-4e30-aa13-f7971f1b671c" />  
R3  
<img width="223" height="80" alt="Screenshot 2026-08-17 031534" src="https://github.com/user-attachments/assets/7ef4d1f1-2529-4e17-a2dc-cc3b97f2d46f" />  

Ketika ketiga router sudah dikonfigurasikan RIP, maka mereka akan saling bertukar
informasi jaringan yang terhubung langsung pada mereka satu sama lain, sehingga setiap
router mengetahui setiap jaringan yang juga terhubung pada router di sebelelahnya.

```
#do show ip route
> Menampilkan daftar routing pada setiap router setelah dikonfigurasi
```
R1  
<img width="413" height="198" alt="Screenshot 2026-08-17 031816" src="https://github.com/user-attachments/assets/f127adce-0585-4bdf-8d4f-20e13083dd74" />  
R2  
<img width="396" height="215" alt="Screenshot 2026-08-17 031855" src="https://github.com/user-attachments/assets/5f4b35e4-ce02-4794-8dd3-129d6b5a3812" />  
R3  
<img width="413" height="197" alt="Screenshot 2026-08-17 031920" src="https://github.com/user-attachments/assets/6afcce7d-f0cd-42f3-9b95-e3230643f5da" />  

Dapat dilihat pada gambar di atas, setiap router telah saling mengetahui informasi
setiap jaringan yang terhubung pada setiap router ditandai dengan kode R (RIP) pada tabel
routing yang ditampilkan di setiap router. Maka dengan demikian setiap jaringan yang ada
pada topologi tersebut telah saling terkoneksi satu sama lain melalui protokol RIP pada
setiap router yang menghubungkan mereka. Untuk memastikannya, Anda bisa melakukan
test ping pada PC yang berada pada satu jaringan ke PC jaringan lain yang terhubung pada
router yang berbeda seperti pada gambar berikut.  

<img width="328" height="273" alt="Screenshot 2026-08-17 032129" src="https://github.com/user-attachments/assets/0c108c7a-0893-41e5-93cd-18bdb5e95e69" />  


## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer

