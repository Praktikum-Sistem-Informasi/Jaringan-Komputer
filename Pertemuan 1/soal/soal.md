# Soal Praktikum — Pertemuan 1: Pengenalan IP Address, Topologi Jaringan, dan Subnetting

## 📋 Deskripsi Tugas
Challenge ini bertujuan untuk memperkuat fundamental praktikan dalam **menghitung subnetting secara manual** serta pemahaman teori dasar IP Address dan Topologi Jaringan.

## 🧰 Alat & Bahan
- Kalkulator dan kertas kerja untuk perhitungan manual
- Modul referensi **Pertemuan 1: Pengenalan IP Address, Topologi Jaringan, dan Subnetting**

## 📝 Bagian 1: Latihan Perhitungan Subnetting Manual
Kerjakan perhitungan subnetting berikut secara manual (tuliskan rumus, proses hitung, dan hasil akhir — bukan hanya jawaban akhir). Gunakan rumus pada modul:
```
Total IP     = 2^(32 - CIDR)
Usable Host  = Total IP - 2
NA           = (Nilai Oktet IP ÷ Total IP) × Total IP
BC           = NA + Total IP - 1
Host Range   = (NA + 1)  sampai  (BC - 1)
SM           = 255.255.255.256 - Total IP
```

Tentukan **Total IP, Usable Host, Network Address, Broadcast Address, Host Range, dan Subnet Mask** dari alamat IP berikut:

1. `192.168.5.70/26`
2. `172.16.30.100/27`

## 📝 Bagian 2: Menentukan CIDR dari Kebutuhan Host
Tentukan CIDR yang paling sesuai (paling efisien) untuk sebuah ruang laboratorium yang membutuhkan jaringan untuk 20 perangkat, lalu buktikan dengan perhitungan Usable Host!

## 📝 Bagian 3: Pertanyaan Teori
1. Jelaskan perbedaan antara Network Address, Host Address, dan Broadcast Address!
2. Jelaskan perbedaan Private IP Address dan Public IP Address, serta sebutkan salah satu rentang Private IP Address!
3. Jelaskan perbedaan topologi Star dan topologi Bus, lengkap dengan satu kelebihan dan satu kekurangan masing-masing!

## 📤 Ketentuan Pengumpulan
- Jawaban Bagian 1 dan Bagian 2 ditulis lengkap dengan proses perhitungan (rumus, hitung, hasil) dan dikumpulkan dalam bentuk gambar berisi hasil pengerjaan.
- Semua soal dikerjakan dan dikumpulkan di Google Form.
- Link pengumpulan: [link gform]
- Batas waktu pengumpulan: [tanggal]
