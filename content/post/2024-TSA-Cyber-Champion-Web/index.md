+++
date = '2024-11-10T19:00:07+07:00'
draft = false
title = '[TSA Cyber Champion 2024] - Web Exploit 101 Writeup'
tags = [
    "LFI",
    "Web Exploit",
    "Command Injection",
    "File Upload",
    "RCE",
]
categories = [
    "Web Exploit",
    "CTF Writeup"
]
image = "image-8.png"
+++

## Deskripsi Soal

![Deskripsi Soal](image-1.png)

Soal ini hanya memberikan sebuah tautan (link) website tanpa deskripsi tambahan, sehingga pendekatan _blackbox testing_ diperlukan.

URL: [https://cyberchampion-web-101.chals.io/](https://cyberchampion-web-101.chals.io/)

Mirror: [http://103.196.154.155:20000/](http://103.196.154.155:20000/)

Tampilan awal website:

![Tampilan Awal Website](image-2.png)

Website ini memiliki dua menu utama: "Ping" dan "File Up".

## Analisis Awal

### Menu Ping

Menu "Ping" memiliki dua potensi kerentanan:

1.  **Local File Inclusion (LFI):** Parameter `page` pada URL `https://cyberchampion-web-101.chals.io/index.php?page=ping.php` terlihat mencurigakan dan mungkin rentan terhadap LFI.
2.  **Command Injection:** Input pada fitur "Ping" mungkin rentan terhadap Command Injection.

![Menu Ping](image-3.png)

### Eksplorasi LFI

Mencoba mengakses URL `https://cyberchampion-web-101.chals.io/index.php?page=` menghasilkan pesan error yang mengungkapkan path website: `/var/www/html/pages/`. Ini mengonfirmasi bahwa website menggunakan fungsi `include()` untuk memanggil file.

![Error Path Website](image-4.png)

Dengan informasi ini, dicoba melakukan _path traversal_ untuk mengakses file sensitif, seperti `/etc/passwd`.

`https://cyberchampion-web-101.chals.io/index.php?page=../../../../etc/passwd`

![Eksploitasi LFI Berhasil](image-5.png)

Eksploitasi LFI berhasil, yang berarti website ini memiliki celah LFI.

### Percobaan yang Gagal

Beberapa percobaan berikut dilakukan, namun tidak berhasil:

- Mencoba menggunakan wrapper dengan Burp Suite dan ekstensi Hackbar.

  ![Percobaan Wrapper Gagal](image-6.png)

- Mencoba mengakses `/proc/self/environ` (berdasarkan referensi dari [https://www.exploit-db.com/papers/12886](https://www.exploit-db.com/papers/12886)).

  ![Percobaan /proc/self/environ Gagal](image-7.png)

### Eksplorasi Command Injection

Mencoba fitur "Ping" dengan input `google.com`.

![Fitur Ping dengan Input google.com](image-8.png)

Fitur ini tampaknya mengeksekusi perintah "ping". Dicoba melakukan Command Injection dengan input:

`google.com; id`

![Percobaan Command Injection](image-9.png)

Input ini seharusnya mengeksekusi perintah `ping` dan `id`. Jika dicoba di Linux, hasilnya akan seperti ini:

![Hasil Command Injection di Linux](image-10.png)

Namun, ketika dicoba pada website, hasilnya kosong (blank).

![Hasil Command Injection di Website](image-11.png)

Berbagai upaya _bypass_ dicoba, tetapi tidak berhasil.

### Menu File Up

Karena upaya sebelumnya tidak berhasil, dicoba menu "File Up".

![Menu File Up](image-12.png)

Mencoba mengunggah file gambar.

![Mengunggah File Gambar](image-13.png)

Setelah di-submit, tidak ada informasi apakah file berhasil diunggah atau tidak, dan tidak ada informasi lokasi file jika berhasil diunggah.

### Eksploitasi File Upload

Menggunakan Burp Suite untuk menganalisis proses upload. Awalnya, mencoba mengunggah file dengan nama yang sama ("sad.jpg"), tetapi gagal. Kemudian, mencoba dengan nama "sad1.jpg" dan berhasil.

![Upload File Berhasil](image-14.png)

Pesan "Image Successfully uploaded!" dan tautan ke file yang diunggah diberikan:

`http://cyberchampion-web-101.chals.io/uploads/b234baed21817d689650251d80d39293.jpg`

Mengakses tautan tersebut menunjukkan bahwa file berhasil diunggah.

![File yang Diunggah Berhasil Diakses](image-15.png)

## Eksploitasi Lanjutan (LFI to RCE)

Mengacu pada artikel tentang memanfaatkan LFI untuk mencapai RCE:

[https://outpost24.com/blog/from-local-file-inclusion-to-remote-code-execution-part-2/](https://outpost24.com/blog/from-local-file-inclusion-to-remote-code-execution-part-2/)

Artikel tersebut menjelaskan bagaimana menggabungkan LFI dan File Upload untuk mencapai RCE. Kode PHP disisipkan ke dalam file gambar, kemudian diunggah. Selanjutnya, LFI digunakan untuk mengakses file gambar tersebut dan mengeksekusi kode PHP yang ada di dalamnya.

### Bypass dengan Header "GIF98a;"

Mencoba _bypass_ dengan menyisipkan header "GIF98a;" (berdasarkan referensi dari [https://github.com/malware-d/template/blob/master/example_attack/Bypassing%20File%20Upload%20Restrictions.md](https://github.com/malware-d/template/blob/master/example_attack/Bypassing%20File%20Upload%20Restrictions.md)).

![Menyisipkan Header GIF98a](image-16.png)

File `sad.jpg` diubah dengan menghapus semua isinya dan menggantinya dengan kode PHP yang disisipkan header "GIF98a;".

![Modifikasi File sad.jpg](image-17.png)

Mencoba mengakses file melalui LFI dengan menambahkan parameter `cmd`:

`https://cyberchampion-web-101.chals.io/index.php?page=../uploads/9c090a873b2789d317eb5bb5fe165329.jpg?cmd=id`

Hasilnya gagal.

![Percobaan LFI dengan Parameter cmd Gagal](image-18.png)

Kemudian, mencoba dengan format yang berbeda:

`https://cyberchampion-web-101.chals.io/index.php?page=../uploads/9c090a873b2789d317eb5bb5fe165329.jpg&cmd=id`

![Percobaan LFI Berhasil](image-19.png)

![Hasil Eksekusi Perintah id](image-20.png)

Eksploitasi berhasil! Sekarang, dicoba melakukan reverse shell untuk mendapatkan akses yang lebih leluasa.

### Reverse Shell

Menggunakan `ngrok` untuk melakukan port forwarding.

![Ngrok](image-21.png)

Port 1337 diaktifkan.

![Port 1337 Aktif](image-22.png)

Menggunakan reverse shell dari Pentest Monkey ([https://www.revshells.com/](https://www.revshells.com/)).

![Reverse Shell dari Pentest Monkey](image-23.png)

Skrip reverse shell disesuaikan dengan port yang diberikan oleh `ngrok`.

![Skrip Reverse Shell](image-24.png)

Skrip PHP yang diunggah diubah dengan skrip reverse shell. Kemudian, diakses melalui LFI.

![Akses LFI dengan Reverse Shell](image-25.png)

Netcat mendengarkan pada port 1337.

![Netcat Menerima Koneksi](image-26.png)

Reverse shell berhasil!

### Mencari Flag

Setelah mendapatkan reverse shell, dicari file yang berisi flag.

![Mencari File Flag](image-27.png)

Ditemukan file bernama `flag`. Langsung saja dicek isinya.

![Isi File Flag](image-28.png)

## Solusi

**Flag:** `TSA{Web_Hacking_101_c7319b0bd96f9d01981bbf52ebb7027f}`
