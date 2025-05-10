+++
date = '2024-10-12T16:54:04+07:00'
draft = false
title = '[Cyber Strike Competition 1.0 CTF 2024] - Forensic'
categories = [
    "Forensic",
    "CTF Writeup"
]
image = "image-21.png"
+++

# Writeup Forensic CTF

![Cyber Strike Competition 1.0](image-1.png)

## Log yang Tersembunyi

### Deskripsi Soal

Pada soal ini, saya diberikan sebuah file zip yang berisi sebuah petunjuk tersembunyi.

![Soal Log yang Tersembunyi](image-4.png)

### Analisis dan Pemecahan Masalah

Untuk menyelesaikan soal ini, langkah pertama yang saya lakukan adalah mengekstrak file zip tersebut dan menyimpannya ke dalam folder "forensic".

![Ekstraksi File Zip](image-5.png)

Setelah diekstrak, ternyata hanya ada satu file, yaitu "important.txt".

![File important.txt](image-6.png)

Namun, setelah saya periksa, isi dari file "important.txt" tidak memberikan informasi apapun. Karena tidak ada file lain, saya mencoba melakukan pengecekan pada file zip itu sendiri menggunakan "xxd".

![Pengecekan File Zip dengan xxd](image-7.png)

Ternyata, di dalam file zip tersebut terdapat sebuah flag!

![Flag pada File Zip](image-8.png)

### Solusi

**Flag:** `CTF{askdjaskjd123280912}`

## Mystery in the Files

### Deskripsi Soal

Pada soal ini, saya diberikan sebuah file `barangbukti.zip` yang berisi banyak subfolder dan file teks sesuai dengan deskripsi soal.

![Soal Mystery in the Files](image-9.png)

### Analisis dan Pemecahan Masalah

Setelah mengekstrak file `barangbukti.zip`, saya menemukan banyak subfolder dan file teks.

![Isi File barangbukti.zip](image-10.png)

Jika saya periksa satu per satu, tentu akan memakan waktu. Oleh karena itu, saya memutuskan untuk mencari kata "CTF" pada setiap file txt dengan bantuan "grep".

![Pencarian Kata CTF dengan grep](image-12.png)

Namun, hasilnya nihil. Tidak ada kata "CTF{" yang ditemukan.

Kemudian, saya mencoba mencari dengan kata "FLAG".

![Pencarian Kata FLAG dengan grep](image-13.png)

Hasilnya, saya menemukan sebuah flag, yaitu "FLAG{Forensic_Expert_123}". Karena format flag yang diminta adalah CTF{}, maka flag yang benar adalah CTF{Forensic_Expert_123}.

### Solusi

**Flag:** `CTF{Forensic_Expert_123}`

## Labirin Data Terlupakan

### Deskripsi Soal

Pada soal ini, saya diberikan sebuah file zip yang berisi banyak subfolder dan file txt di setiap subfoldernya.

![Soal Labirin Data Terlupakan](image-14.png)

### Analisis dan Pemecahan Masalah

Setelah mengekstrak file zip tersebut, saya menemukan struktur folder yang kompleks.

![Isi File Zip](image-15.png)

Saya mencoba mencari kata "flag" dan "CTF{" dengan grep, tetapi hasilnya tetap tidak ditemukan.

![Pencarian Kata dengan grep](image-16.png)

Kemudian, saya mencoba mengikuti steg-check dari [https://book.hacktricks.xyz/crypto-and-stego/stego-tricks](https://book.hacktricks.xyz/crypto-and-stego/stego-tricks), terutama fokus pada penggunaan foremost.

![Steg Check](image-17.png)

Saya langsung mencoba menggunakan foremost.

![Penggunaan Foremost](image-18.png)

Pada tampilan hasil foremost, tidak ada informasi yang menarik. Namun, saya menemukan adanya folder output.

![Folder Output Foremost](image-19.png)

Di dalam folder output, ternyata ada folder "jpg". Ini menandakan bahwa di dalam zip tadi terdapat file jpg. Berikut adalah isi dari file `audit.txt` yang dihasilkan oleh foremost.

![Isi File audit.txt](image-20.png)

Setelah itu, saya langsung mengecek file jpg tersebut.

Jika dibuka, gambar jpg tersebut hanya menampilkan gambar doraemon.

![Gambar Doraemon](image-21.png)

Kemudian, saya mulai mencoba melakukan stegano check, seperti menggunakan `file`, `exiftool`, dan `strings`.

![Stegano Check](image-22.png)

Pada bagian awal string, saya menemukan pola seperti ini:

```
*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
```

Saya menduga ini adalah bagian key yang mungkin berguna untuk langkah selanjutnya.

Karena dari percobaan tadi tidak ada informasi yang menarik, saya mencoba menggunakan steghide.

![Penggunaan Steghide](image-23.png)

Namun, ternyata steghide meminta password.

Saya mencoba menggunakan key yang saya temukan sebelumnya, tetapi tidak berhasil.

Oleh karena itu, saya perlu mencari password untuk membuka isi dari gambar tersebut. Untuk mencari password/passphrase, saya menggunakan tools StegCracker ([https://github.com/Paradoxis/StegCracker](https://github.com/Paradoxis/StegCracker)) untuk melakukan _brute force_ passphrase.

![Penggunaan StegCracker](image-24.png)

Secara otomatis, StegCracker akan menggunakan wordlist dari `rockyou.txt` yang tersimpan di PC. Saya langsung menjalankan StegCracker.

![Menjalankan StegCracker](image-25.png)

Setelah beberapa saat, saya berhasil menemukan password untuk gambar tersebut, yaitu "password123". Sekarang, saya mencoba lagi steghide dengan password/passphrase "password123".

![Penggunaan Steghide dengan Password](image-26.png)

Ternyata, di dalam gambar tersebut masih ada file txt lagi. Sekarang, saya akan mengecek file txt tersebut.

![Isi File txt](image-27.png)

Akhirnya, saya menemukan flag-nya!

### Solusi

**Flag:** `CTF{labirin_disini}`

## Tersembunyi

### Deskripsi Soal

Pada soal kali ini, saya ditugaskan untuk mencari flag di dalam file zip.

![Soal Tersembunyi](image-28.png)

### Analisis dan Pemecahan Masalah

Saya langsung mengekstrak file zip tersebut.

![Ekstraksi File Zip](image-29.png)

Dari hasil ekstraksi, terdapat dua file txt. Berikut adalah isi dari masing-masing file txt.

![Isi File txt](image-30.png)

Ada sesuatu yang menarik pada file `secret_message.txt`.

Pada bagian "pesan penting", terdapat kode biner. Saya mencoba mendekode kode biner ini menggunakan web [https://cryptii.com/pipes/binary-decoder](https://cryptii.com/pipes/binary-decoder), tetapi hasilnya tidak bisa didekode.

![Dekode Kode Biner dengan cryptii](image-31.png)

Kemudian, saya menggunakan web favorit saya, yaitu CyberChef, untuk menggunakan fitur "Magic" yang secara otomatis dapat mendeteksi encoding yang digunakan.

![Penggunaan CyberChef](image-32.png)

Hasilnya, encoding yang digunakan adalah "UNIX Timestamp", dan hasilnya berupa tanggal:

`Mon 14 January 2002 12:08:21.100 UTC`

Selanjutnya, saya mencoba mendekode bagian "Flag terenkripsi: Q1RGezEyM1NyeXB0b30=". Saya masih menggunakan CyberChef.

![Dekode Flag dengan CyberChef](image-33.png)

Hasilnya menampilkan flag!

### Solusi

**Flag:** `CTF{123Srypto}`
