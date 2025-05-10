+++
date = '2024-10-12T16:54:04+07:00'
draft = false
title = '[Cyber Strike Competition 1.0 CTF 2024] - Stegano'
categories = [
    "Stegano",
    "CTF Writeup"
]
image = "image-43.png"
+++

![Cyber Strike Competition 1.0](image-1.png)

# Stegano

## Eksplorasi Gambar Terenkripsi

Pada write up ini dibuat, sayangnya kondisi event ctf pada website nya sudah tutup, sehingga saya lupa untuk melakukan screenshoot soal nya.

Intinya pada soal kali ini, terdapat sebuah file gambar .png. Untuk mendapatkan flag nya perlu mencari di dalam gambar tersebut. Tampilan dari ctf.png ini hanya berupa tampilan warna putih seperti dibawah berikut:

![image-41](image-41.png)

Disini hal yang saya lakukan yaitu mengecek informasi pada gambar tersebut, seperti ‘file’, ‘exiftool’, dan ‘strings’.

![image-42](image-42.png)

Dari hasil informasi, sebenarnya tidak ada yang menarik. Tetapi dari informasi tersebut bahwa file ctf.png ini memang benar format PNG.

Lalu saya mencoba mengikuti metode dari stegano-tricks https://book.hacktricks.xyz/crypto-and-stego/stego-tricks

Fokus pada bagian zsteg, karna format file ini yaitu png.

![image-43](image-43.png)

Hasil dari zsteg, dapat menampilkan sebuah flag

![image-44](image-44.png)

flag: CTF{hidden_flag}
