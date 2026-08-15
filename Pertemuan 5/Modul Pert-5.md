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
Router(config)#
```
## 📖 Materi Praktikum
### 1. Dynamic Routing EIGRP
EIGRP adalah protokol routing dinamis buatan Cisco yang tergolong advanced distance-vector (hybrid), karena menggabungkan karakteristik distance-vector dengan efisiensi link-state. EIGRP menggunakan algoritma DUAL (Diffusing Update Algorithm) untuk menghitung jalur terbaik (successor) sekaligus menyiapkan jalur cadangan (feasible successor), sehingga proses convergence saat terjadi perubahan topologi menjadi sangat cepat.

Metrik EIGRP bersifat komposit, dihitung dari kombinasi bandwidth, delay, reliability, dan load (secara default hanya bandwidth dan delay yang dipakai). Berbeda dari RIP, EIGRP tidak mengirim update routing secara periodik penuh, melainkan hanya mengirim update parsial saat terjadi perubahan (triggered update), sehingga lebih efisien pada jaringan besar. Agar dua router dapat bertetangga, keduanya harus berada dalam Autonomous System (AS) number yang sama

### 2. Dynamic Routing OSPF
OSPF adalah protokol routing dinamis berbasis link-state dan merupakan standar terbuka (RFC 2328), sehingga dapat digunakan lintas vendor perangkat jaringan. Setiap router OSPF saling bertukar LSA (Link State Advertisement) untuk membentuk LSDB (Link State Database) yang identik di seluruh router dalam satu area. Dari LSDB tersebut, setiap router menghitung jalur terpendek secara independen menggunakan algoritma Dijkstra (Shortest Path First).

Metrik OSPF disebut cost, yang dihitung berdasarkan bandwidth interface (semakin besar bandwidth, semakin kecil cost, semakin disukai jalurnya). Untuk menjaga skalabilitas pada jaringan besar, OSPF membagi jaringan ke dalam beberapa area, dengan Area 0 (backbone area) sebagai area utama yang menghubungkan area-area lain. Router OSPF saling mengenali tetangganya melalui pertukaran paket secara periodik untuk membentuk dan menjaga neighbor adjacency.

## 📝 Catatan
- Deadline pengumpulan: [tanggal]
- Asisten yang membawakan: [nama]

## 📚 Referensi
- Modul Praktikum Jaringan Komputer


