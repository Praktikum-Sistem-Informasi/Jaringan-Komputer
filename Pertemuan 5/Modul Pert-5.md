# Pertemuan 5: Dynamic Routing (EIGRP & OSPF)

## 🎯 Tujuan Pembelajaran
- Praktikan mampu memahami konsep dasar dynamic routing serta perbedaan karakteristik antara protokol EIGRP dan OSPF.
- Praktikan mampu memahami mekanisme kerja algoritma DUAL pada EIGRP dalam menentukan jalur utama (successor) dan jalur cadangan (feasible successor).
- Praktikan mampu mengonfigurasi dynamic routing menggunakan EIGRP pada topologi multi router.
- Praktikan mampu memahami konsep link-state, LSA, dan pembagian area pada protokol OSPF.
- Praktikan mampu mengonfigurasi dynamic routing menggunakan OSPF pada topologi multi router dengan pembagian area yang sesuai.
- Praktikan mampu melakukan verifikasi dan troubleshooting tabel routing EIGRP dan OSPF menggunakan perintah `show ip route`, `show ip eigrp neighbors`, dan `show ip ospf neighbor` pada CLI Cisco.


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
### 1. Konfigurasi dan Penjelasan Dynamic Routing EIGRP
EIGRP adalah protokol routing dinamis buatan Cisco yang tergolong advanced distance-vector (hybrid), karena menggabungkan karakteristik distance-vector dengan efisiensi link-state. EIGRP menggunakan algoritma DUAL (Diffusing Update Algorithm) untuk menghitung jalur terbaik (successor) sekaligus menyiapkan jalur cadangan (feasible successor), sehingga proses convergence saat terjadi perubahan topologi menjadi sangat cepat.

Metrik EIGRP bersifat komposit, dihitung dari kombinasi bandwidth, delay, reliability, dan load (secara default hanya bandwidth dan delay yang dipakai). Berbeda dari RIP, EIGRP tidak mengirim update routing secara periodik penuh, melainkan hanya mengirim update parsial saat terjadi perubahan (triggered update), sehingga lebih efisien pada jaringan besar. Agar dua router dapat bertetangga, keduanya harus berada dalam Autonomous System (AS) number yang sama.
## Cheatsheet CLI Konfigurasi Dynamic Routing EIGRP

| Fungsi                    | Perintah CLI                                               | Penjelasan                                                                                                 |
|---------------------------|------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| Masuk mode konfigurasi EIGRP | `Router(config)# router eigrp (Routing ID)`                | Mengaktifkan proses EIGRP dengan nomor Autonomous System tertentu; seluruh router dalam satu domain harus pakai AS number yang sama. |
| Mendaftarkan network      | `Router(config-router)# network (NA jaringan yang terhubung langsung pada router)` | Mendaftarkan network yang terhubung langsung agar ikut diiklankan lewat EIGRP.                             |
| Menonaktifkan auto-summary | `Router(config-router)# no auto-summary`                   | Mencegah EIGRP meringkas network ke classful boundary, penting saat pakai subnetting/VLSM.                 |
| Menghapus proses EIGRP    | `Router(config)# no router eigrp (Routing ID)`              | Menonaktifkan seluruh proses EIGRP pada router.                                                            |
| Melihat tabel routing     | `Router# show ip route`                                    | Menampilkan seluruh route aktif, termasuk route hasil EIGRP (kode D).                                      |
| Melihat tetangga EIGRP    | `Router# show ip eigrp neighbors`                          | Menampilkan daftar router tetangga yang sudah membentuk adjacency lewat EIGRP.                             |
| Melihat topology table    | `Router# show ip eigrp topology`                           | Menampilkan seluruh jalur yang diketahui EIGRP, termasuk successor dan feasible successor.                 |
| Melihat status protokol   | `Router# show ip protocols`                                | Menampilkan detail proses EIGRP aktif: AS number, network yang didaftarkan, dan sumber route.              |

#### Langkah kerja:  
<img width="565" height="300" alt="Screenshot 2026-08-18 004650" src="https://github.com/user-attachments/assets/7c405571-a207-4577-8ced-546f126cd62c" />  


Pada dasarnya setiap protokol dynamic routing memiliki cara kerja yang sama, hanya
saja terdapat perbedaan pada aturan atau matrik yang diterapkan dalam menentukan jalur
perutean. Serupa pada pembahasan protokol RIP sebelumnya, pada toopologi di atas,
terdapat tiga buah perangkat router yang terkoneksi satu sama lain. Ketika Anda
melakukan konfigurasi EIGRP pada ketiga Router yang ada, maka setiap router akan
mengirimkan informasi berupa alamat jaringan yang terhubung dan terdaftar pada tabel
routing mereka ke router yang berada di sebelahnya dan begitu pula sebaliknya.

```
#do show ip route
> Menampilkan daftar routing pada setiap router
```
R1  
<img width="565" height="300" alt="Screenshot 2026-08-18 010021" src="https://github.com/user-attachments/assets/911baf5e-ff22-4219-920a-2f019bdd330f" />  
R2  
<img width="565" height="300" alt="Screenshot 2026-08-18 010051" src="https://github.com/user-attachments/assets/5e7086eb-5b03-4aaa-b81e-1fb7a02419be" />  
R3  
<img width="565" height="300" alt="Screenshot 2026-08-18 010115" src="https://github.com/user-attachments/assets/ddadf68f-7641-4132-a1a7-6064fc82e597" />  


Dapat dilihat pada gambar di atas, sebelum dilakukan konfigurasi routing EIGRP pada
ketiga router hanya mendeteksi jaringan yang terhubung secara langsung (default) pada
mereka saja, namun belum mengetuhi jaringan yang terhubung pada router lain.
Selanjutnya Anda bisa melakukan konfigurasi EIGRP pada setiap router dengan
konfigurasi berikut.

```
#router eigrp (Routing ID)
> Menginisiasi konfigurasi routing dengan protokol EIGRP
```

```
#network (NA jaringan yang terhubung langsung pada router)
> Konfigurasi routing dengan protokol EIGRP
```

R1  
<img width="565" height="150" alt="Screenshot 2026-08-18 011101" src="https://github.com/user-attachments/assets/a0943a93-bbaf-48e3-9940-dc0a247b508f" />  
R2  
<img width="565" height="150" alt="Screenshot 2026-08-18 011156" src="https://github.com/user-attachments/assets/acda74fd-bce4-428b-91c6-c412ca68ed2c" />  
R3  
<img width="565" height="150" alt="Screenshot 2026-08-18 011228" src="https://github.com/user-attachments/assets/965b786b-a154-4c79-ade4-607585dbc967" />  

Pada protokol routing EIGRP, router Cisco akan mengimplementasikan routing ID
sebagai autentikasi pengelompokan routing yang akan dilakukan. Router dengan
konfigurasi routing EIGRP nantinya hanya akan membagikan informasi jaringan yang
terdaftar pada tabel routing mereka ke router lain di sebelahnya dengan konfigurasi routing
EIGRP pada ID yang sama saja.
Ketika ketiga router sudah dikonfigurasikan routing EIGRP dengan ID routing yang
sama, maka mereka akan saling bertukar informasi jaringan yang terhubung langsung pada
mereka satu sama lain, sehingga setiap router mengetahui setiap jaringan yang juga
terhubung pada router di sebelelahnya. (btw untuk notifikasi *%DUAL-5-NBRCHANGE: IP-EIGRP...* merupakan pertanda bahwa routing telah berhasil dan sudah saling terhubung)

```
#do show ip route
> Menampilkan daftar routing pada setiap router setelah dikonfigurasi
```

R1  
<img width="565" height="300" alt="Screenshot 2026-08-18 012437" src="https://github.com/user-attachments/assets/64691495-a6ad-469b-b825-8af19d88ebf4" />  
R2  
<img width="565" height="300" alt="Screenshot 2026-08-18 012455" src="https://github.com/user-attachments/assets/90715999-8294-47dd-a240-a6538536a5a8" />  
R3  
<img width="565" height="300" alt="Screenshot 2026-08-18 012533" src="https://github.com/user-attachments/assets/5fefaabf-5886-436a-87ae-498ec32e8c7d" />  

Dapat dilihat pada gambar di atas, setiap router telah saling mengetahui informasi
setiap jaringan yang terhubung pada setiap router ditandai dengan kode D (EIGRP) pada
tabel routing yang ditampilkan di setiap router. Maka dengan demikian setiap jaringan
yang ada pada topologi tersebut telah saling terkoneksi satu sama lain melalui protokol
EIGRP dengan routing ID 10 pada setiap router yang menghubungkan mereka. Untuk
memastikannya, Anda bisa melakukan test ping pada PC yang berada pada satu jaringan
ke PC jaringan lain yang terhubung pada router yang berbeda seperti pada gambar berikut.

<img width="565" height="350" alt="Screenshot 2026-08-18 012722" src="https://github.com/user-attachments/assets/e0f5caae-2d75-4671-89d9-ce8be74cb219" />


### 2. Konfigurasi dan Penjelasan Dynamic Routing OSPF
OSPF adalah protokol routing dinamis berbasis link-state dan merupakan standar terbuka (RFC 2328), sehingga dapat digunakan lintas vendor perangkat jaringan. Setiap router OSPF saling bertukar LSA (Link State Advertisement) untuk membentuk LSDB (Link State Database) yang identik di seluruh router dalam satu area. Dari LSDB tersebut, setiap router menghitung jalur terpendek secara independen menggunakan algoritma Dijkstra (Shortest Path First).

Metrik OSPF disebut cost, yang dihitung berdasarkan bandwidth interface (semakin besar bandwidth, semakin kecil cost, semakin disukai jalurnya). Untuk menjaga skalabilitas pada jaringan besar, OSPF membagi jaringan ke dalam beberapa area, dengan Area 0 (backbone area) sebagai area utama yang menghubungkan area-area lain. Router OSPF saling mengenali tetangganya melalui pertukaran paket secara periodik untuk membentuk dan menjaga neighbor adjacency.
## Cheatsheet CLI Konfigurasi Dynamic Routing OSPF

| Fungsi                    | Perintah CLI                                                            | Penjelasan                                                                                                 |
|---------------------------|-------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| Masuk mode konfigurasi OSPF | `Router(config)# router ospf (Routing ID)`                             | Mengaktifkan proses OSPF dengan process-id tertentu; process-id bersifat lokal, tidak perlu sama di tiap router. |
| Mendaftarkan network      | `Router(config-router)# network(NA jaringan yang terhubung langsung pada router) (Wildcard Mask) (Area ID)` | Mendaftarkan network yang terhubung langsung beserta area OSPF-nya (biasanya area 0/backbone).            |
| Mengatur router-id        | `Router(config-router)# router-id (id)`                                 | Menentukan identitas unik router dalam domain OSPF (format mirip IP address), berguna untuk pemilihan DR/BDR. |
| Mengubah cost interface   | `Router(config-if)# ip ospf cost (nilai)`                               | Mengatur manual nilai cost pada suatu interface, menggantikan hasil hitungan otomatis dari bandwidth.      |
| Menghapus proses OSPF     | `Router(config)# no router ospf (process_id)`                           | Menonaktifkan seluruh proses OSPF pada router.                                                             |
| Melihat tabel routing     | `Router# show ip route`                                                 | Menampilkan seluruh route aktif, termasuk route hasil OSPF (kode O).                                       |
| Melihat tetangga OSPF     | `Router# show ip ospf neighbor`                                         | Menampilkan daftar router tetangga yang sudah membentuk adjacency lewat OSPF.                              |
| Melihat database OSPF     | `Router# show ip ospf database`                                         | Menampilkan seluruh LSA yang tersimpan di Link State Database (LSDB).                                      |
| Melihat status protokol   | `Router# show ip protocols`                                             | Menampilkan detail proses OSPF aktif: router-id, area, network yang didaftarkan, dan sumber route.         |

#### Langkah kerja:  
<img width="565" height="300" alt="Screenshot 2026-08-18 004650" src="https://github.com/user-attachments/assets/7c405571-a207-4577-8ced-546f126cd62c" />  

Pada konfigurasi routing OSPF, selain memperhatikan ID routing yang digunakan
pada setiap router, Anda juga harus menetapkan area routing yang digunakan. Pada
protokol OSPF router hanya akan membagikan informasi yang terdapat pada databasenya
pada jaringan-jaringan yang terdaftar pada area yang sama saja di setiap router-nya.
Berikut adalah konfigurasi selengkapnya.

```
#do show ip route
> Menampilkan daftar routing pada setiap router
```
R1  
<img width="565" height="300" alt="Screenshot 2026-08-18 010021" src="https://github.com/user-attachments/assets/911baf5e-ff22-4219-920a-2f019bdd330f" />  
R2  
<img width="565" height="300" alt="Screenshot 2026-08-18 010051" src="https://github.com/user-attachments/assets/5e7086eb-5b03-4aaa-b81e-1fb7a02419be" />    
R3    
<img width="565" height="300" alt="Screenshot 2026-08-18 010115" src="https://github.com/user-attachments/assets/ddadf68f-7641-4132-a1a7-6064fc82e597" />  

```
#router ospf (Routing ID)
> Menginisiasi konfigurasi routing dengan protokol OSPF
```

```
#network (NA jaringan yang terhubung langsung pada router)
(Wildcard Mask) (Area ID)
> Konfigurasi routing dengan protokol OSPF
```
R1  
<img width="565" height="67" alt="Screenshot 2026-08-18 013313" src="https://github.com/user-attachments/assets/b789c9c2-88e3-4bfb-ae51-dd0949c3a8a8" />  
R2  
<img width="565" height="105" alt="Screenshot 2026-08-18 013449" src="https://github.com/user-attachments/assets/f435dc76-051b-4e8e-be83-1ae8bf64eb53" />  
R3   
<img width="565" height="67" alt="Screenshot 2026-08-18 013601" src="https://github.com/user-attachments/assets/4a29ee1a-2e01-4296-9c8b-a8c083344432" />

Wildcard Mask adalah sederet angka yang menentukan range IP untuk diizinkan
pada suatu jaringan. Subnet mask dan wildcard mask merupakan dua hal yang serupa
namun terdapat perbedaan dalam penulisannya pada sebuah konfigurasi. Ketika ketiga
router sudah dikonfigurasikan routing OSPF dengan ID dan area routing yang sama, maka
mereka akan saling bertukar informasi jaringan yang terhubung langsung pada mereka satu
sama lain dengan area yang sama, sehingga setiap router mengetahui setiap jaringan yang
juga terhubung pada router di sebelelahnya.

```
#do show ip route
> Menampilkan daftar routing pada setiap router setelah dikonfigurasi
```

R1  
<img width="565" height="300" alt="Screenshot 2026-08-18 013745" src="https://github.com/user-attachments/assets/06d65e28-cb84-42e9-8fa7-2e435c723a15" />  
R2  
<img width="565" height="300" alt="Screenshot 2026-08-18 013809" src="https://github.com/user-attachments/assets/3a37c6d0-4175-4316-bbc1-d96a72ba8fe4" />  
R3  
<img width="565" height="300" alt="Screenshot 2026-08-18 013833" src="https://github.com/user-attachments/assets/52a618ba-7119-493b-9b8d-fc9f4bf4cc0b" />


Dapat dilihat pada gambar di atas, setiap router telah saling mengetahui informasi
setiap jaringan yang terhubung pada setiap router ditandai dengan kode O (OSPF) pada
tabel routing yang ditampilkan di setiap router. Maka dengan demikian setiap jaringan
yang ada pada topologi tersebut telah saling terkoneksi satu sama lain melalui protokol
OSPF dengan routing ID 10 dan area 0 pada setiap router yang menghubungkan mereka.
Untuk memastikannya, Anda bisa melakukan test ping pada PC yang berada pada satu
jaringan ke PC jaringan lain yang terhubung pada router yang berbeda seperti pada gambar
berikut.  

<img width="565" height="405" alt="Screenshot 2026-08-18 012722" src="https://github.com/user-attachments/assets/e0f5caae-2d75-4671-89d9-ce8be74cb219" />   

### 3. Latihan Praktikum
1. Rancang topologi ring di Cisco Packet Tracer menggunakan 3 Router, 3 Switch, dan 3 PC, buat dalam dua topologi terpisah pada satu file .pkt (Topologi A dan Topologi B), masing-masing router terhubung langsung ke dua router lainnya!
2. Konfigurasikan Topologi A menggunakan dynamic routing EIGRP (aktifkan router eigrp dengan AS number yang sama di tiap router), dan Topologi B menggunakan dynamic routing OSPF (aktifkan router ospf dengan seluruh network masuk area 0)!
3. Lakukan verifikasi dengan ping dari PC1 ke PC2 dan PC3 pada kedua topologi, lalu bandingkan nilai metric pada show ip route — jelaskan perbedaan cara EIGRP (bandwidth & delay) dan OSPF (cost) dalam menghitung jalur terbaik!
4. Putuskan link R1–R2 pada kedua topologi, lalu ulangi ping dari PC1 ke PC2. Jelaskan analisis Anda mengenai proses reconvergence EIGRP (feasible successor) dibanding OSPF (perhitungan ulang SPF), dan protokol mana yang lebih cepat pulih!



## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer - Pertemuan 5: Dynamic Routing (EIGRP & OSPF)



