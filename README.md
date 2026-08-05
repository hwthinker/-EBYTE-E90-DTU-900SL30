# EBYTE E90-DTU (900SL30) — Konfigurasi RS232/RS485 ke LoRa

![EBYTE E90-DTU (900SL30)](./assets/82853782-55f8-4a4b-9345-2eb91ac97694.jpg)

E90-DTU (900SL30) adalah transceiver data LoRa (chip SX1262) yang mengubah port serial RS232 atau RS485 jadi tautan wireless jarak jauh — dua unit saling terhubung lewat udara, transparan dari sisi perangkat yang dipasang di kedua ujungnya (PC, PLC, RTU, sensor Modbus, dst). Beroperasi di pita frekuensi 850,125–930,125 MHz dengan daya pancar hingga 30 dBm (~1W) dan jangkauan sampai 10 km di ruang terbuka.

## Spesifikasi Singkat

| Parameter | Nilai |
| --- | --- |
| Chip | SX1262 (LoRa) |
| Frekuensi Kerja | 850,125–930,125 MHz (default 868,125 MHz) |
| Daya Transmisi | 30 dBm (~1W), bisa diturunkan ke 27/24/21 dBm |
| Kecepatan Data Udara | 0,3k–62,5 kbps (default 2,4 kbps) |
| Antarmuka | RS232 (DB9) / RS485 (terminal 3,81mm) |
| Antena | SMA-K |
| Jumlah Channel | 81 |
| Sumber Daya | 8–28VDC (rekomendasi 12V atau 24V) |
| Arus Transmisi | ~45mA @ 30dBm |
| Baud Rate | Default 9600, bisa diatur 1200–115200 |
| Dimensi / Berat | 82 × 62 × 25 mm / 130g |

## Yang Perlu Disiapkan

- 2× EBYTE E90-DTU (900SL30) — minimal sepasang untuk uji komunikasi point-to-point
- 2× Antena SMA (atau *dummy load* 50Ω kalau sekadar uji tanpa transmisi udara)
- Adaptor DC 8–28V, atau catu daya bangku
- Kabel USB-ke-RS232 atau USB-ke-RS485, untuk menyambungkan ke PC
- Software **RF Setting** (lihat bagian Software di bawah) — hanya perlu di komputer yang dipakai mengkonfigurasi, bukan wajib terpasang permanen

## Koneksi

### RS232

Port DB-9 female standar — sambungkan langsung ke port RS232 perangkat Anda, atau lewat konverter USB-RS232 ke PC.

### RS485 & Daya — Terminal 3,81mm

| Pin | Nama | Fungsi |
| --- | --- | --- |
| 1 | VCC | Daya masuk, 8–28VDC (positif) |
| 2 | GND | Ground — disambungkan ke ground sistem dan casing |
| 3 | 485_B | RS485 interface B |
| 4 | 485_A | RS485 interface A |

> **Catatan**
> Sambungkan 485_A ke A dan 485_B ke B pada perangkat RS485 lawan (jangan tertukar). Kalau menyambungkan lebih dari dua perangkat di satu bus RS485, disarankan memasang resistor paralel 120Ω di antara terminal 485_A dan 485_B pada kedua ujung bus.

Daya bisa dipasok lewat terminal VCC/GND ini **atau** lewat jack DC (diameter luar 5,5mm, diameter dalam 2,5mm) — pilih salah satu, tidak perlu keduanya.

## Mode Kerja — DIP Switch M0/M1

Ada dua sakelar DIP kecil (M0, M1) di sisi device dekat konektor antena. Kombinasinya menentukan salah satu dari 4 mode kerja:

| Mode | Nama | M1 | M0 | Keterangan |
| --- | --- | --- | --- | --- |
| 0 | Normal | ON | ON | **Mode operasi sehari-hari.** UART dan RF aktif, transparent transmission — data yang masuk lewat serial langsung dipancarkan, dan sebaliknya. |
| 1 | WOR | ON | OFF | Wake-on-Radio — hemat daya, dipakai sebagai transmitter atau receiver WOR. |
| 2 | Konfigurasi | OFF | ON | RF **mati** (tidak bisa kirim/terima data lewat udara). Dipakai khusus untuk mengubah parameter lewat software RF Setting. |
| 3 | Sleep | OFF | OFF | RF mati total, daya paling hemat. |

> **Catatan**
> - Device hanya bisa saling berkomunikasi lewat udara ketika **kedua unit** ada di **Mode 0 (Normal)**.
> - Untuk mengubah parameter (frekuensi, baud rate, alamat, dll), pindahkan dulu ke **Mode 2**, lakukan konfigurasi, lalu **wajib kembalikan ke Mode 0** sebelum device bisa berkomunikasi lagi.
> - Kalau lupa mengembalikan ke Mode 0 setelah konfigurasi, device akan terlihat "menyala tapi bisu" — LED PWR hidup tapi tidak ada data terkirim/diterima.

## Cara Konfigurasi

1. Pasang USB-serial (USB-RS232 atau USB-RS485) ke PC, lalu cek nomor COM-nya di Device Manager.

   ![Cek nomor COM di Device Manager](./assets/image-20251008062804151.png)

2. Ubah posisi DIP switch ke **Mode 2** (Konfigurasi) — lihat tabel mode di atas.

   ![DIP switch di posisi Mode 2](./assets/image-20251008062534190.png)

3. Jalankan **RF Setting**, ubah bahasa ke English kalau perlu, pilih Serial Port yang sesuai, lalu klik **Open**.

   ![Buka RF Setting dan pilih port serial](./assets/image-20251008063155957.png)

4. Klik **Get** untuk membaca parameter saat ini dari device. Kalau berhasil, hasilnya akan tampil seperti ini:

   ![Klik Get untuk membaca parameter](./assets/image-20251008063543351.png)

5. Ubah parameter yang diinginkan (frekuensi, baud rate, alamat, daya pancar, dll), lalu klik **Set** untuk menyimpan ke device. Kalau ingin mengembalikan ke pengaturan pabrik, klik **Param Reset** terlebih dahulu.

   ![Param Reset untuk kembali ke pengaturan pabrik](./assets/image-20251008070108137.png)

6. Setelah selesai konfigurasi, **kembalikan DIP switch ke Mode 0 (Normal)** supaya device bisa saling berkomunikasi lagi dengan device sejenis.

   ![Kembalikan DIP switch ke Mode 0](./assets/image-20251008063746188.png)

> **Catatan**
> - Software RF Setting masih dalam bentuk terkompresi (`.7z`) — ekstrak dulu pakai [7-Zip](https://7-zip.org/download.html) sebelum dijalankan.
> - Konfigurasi lewat RF Setting hanya berfungsi pada baud rate **9600, 8N1** — kalau serial port Anda sudah terlanjur diubah ke baud rate lain, atur dulu balik ke 9600 sebelum connect.

## Catatan Keselamatan

- **Jangan pernah menyalakan device tanpa antena atau dummy load 50Ω terpasang** — memancar tanpa beban yang sesuai berisiko merusak bagian pemancar (transmitter).
- Jaga jarak tubuh minimal 2 meter dari antena saat device sedang memancar.
- Jangan membiarkan device memancar terus-menerus pada beban penuh dalam waktu lama — berisiko membuat pemancar terlalu panas.
- Pastikan ground device tersambung baik ke ground sistem (PC/PLC dan catu daya) — tanpa ini port komunikasi berisiko rusak. Jangan mencolok/mencabut kabel serial saat device menyala.
- Jangan operasikan di dekat area yang mudah terbakar (mis. tambang batu bara) atau dekat benda peledak.

## Software

- **RF Setting** (tool konfigurasi Windows): [software/RF_Setting(E22-E9X(SL)) V3.2.7z](https://github.com/hwthinker/EBYTE-E90-DTU-900SL30/blob/main/software/RF_Setting(E22-E9X(SL))%20V3.2.7z)
- Cadangan: <https://icedrive.net/s/573kCN6XxAjStGNY7va9CG4ikT1R>

## Manual

- **User Manual lengkap (PDF)**: [PDF/E90-DTU(900SL30)_UserManual_EN_v1.2.pdf](https://github.com/hwthinker/EBYTE-E90-DTU-900SL30/blob/main/PDF/E90-DTU(900SL30)_UserManual_EN_v1.2.pdf)
- Cadangan: <https://icedrive.net/s/XTY7YaSXRTbiZ8zhNz7jVbQVDDPz>

## Pemecahan Masalah

**Dua device tidak bisa saling kirim data sama sekali.** Penyebab tersering: salah satu (atau keduanya) masih di Mode 2/3, bukan Mode 0. Cek posisi DIP switch di kedua unit.

**Sudah Mode 0 tapi tetap tidak ada data.** Pastikan frekuensi, channel, baud rate, dan parameter air data rate **sama persis** di kedua unit — beda salah satu saja komunikasi akan gagal total (bukan sekadar lambat).

**RF Setting tidak bisa Get/Set parameter (timeout atau error).** Pastikan: DIP switch di Mode 2 (bukan Mode 0), baud rate port serial di PC diset ke 9600 8N1, dan kabel USB-serial benar-benar tersambung ke port RS232/RS485 device (bukan port lain).

**Jarak komunikasi jauh lebih pendek dari spesifikasi (10 km).** Wajar di lingkungan kota/banyak halangan — jarak riil sangat dipengaruhi obstacle, kelembapan, dan kualitas antena. Cek juga posisi/tinggi pemasangan antena dan pastikan bukan antena yang rusak atau salah frekuensi.

**Device terasa panas atau mati sendiri saat transmit terus-menerus.** Jangan biarkan pada beban penuh (30dBm) dalam durasi lama — turunkan daya pancar lewat RF Setting, atau beri jeda antar pengiriman.
