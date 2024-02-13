---
layout: post
title:  "Measuring biometric feature effectiveness"
author: salmandazmi
categories: [ Portofolio ]
tags: [ Binar Academy ]
image: assets/images/biometric-technology-background.jpg
---
Metode fingerprint biometric scanner sebagai pilihan metode login semakin marak digunakan. Langkah ini dimaksudkan untuk meningkatkan keamaan dan kenyamanan pengguna. Namun, apakah tujuan tersebut tercapai? Bagaimana mengukurnya?

*Artikel ini merupakan solusi dari salah satu kasus bisnis yang diberikan oleh Binar Academy.*

Untuk mengukur kualitas *fingerprint biometric scanner* di aplikasi yang tengah dikembangkan oleh suatu perusahaan, khususnya dalam aspek keamanan, terdapat sejumlah *metrics* yang dapat digunakan. Berikut ini merupakan masing-masing definisi dari *metrics* yang diusulkan.

<!-- # The Metrics -->
## Active Users
*Metric* yang menunjukkan jumlah pengguna yang mengakses aplikasi dalam periode waktu tertentu.

## Biometric Enabled
*Metric* yang menunjukkan jumlah pengguna aktif yang mengaktifkan fitur *fingerprint biometric scanner* di aplikasi.

*Metric* ini dapat mengindikasikan fitur *biometric* yang ada dinilai aman oleh pengguna. Semakin banyak pengguna yang mengaktifkan fitur tersebut, maka tingkat keamanan yang dirasakan oleh pengguna juga meningkat.

## Biometric Usage Rate
Metric yang menunjukkan tingkat penggunaan *biometric* dibandingkan metode login lain untuk masuk ke aplikasi. Dalam hal ini, metode *login* lain yang menjadi alternatif adalah *login* dengan memasukkan *username* dan *password*.

**Perhitungan:** Total # pengguna yang *login* dengan *biometric* / Total # pengguna yang *login*

## Authentication Failure Rate

*Metric* ini menunjukkan tingkat kegagalan fitur *biometric* melakukan autentikasi.

**Perhitungan:** Total # kegagalan autentikasi / Total # percobaan autentikasi

Tingkat kegagalan autentikasi yang tinggi dapat mengindikasikan bahwa pengguna sebenarnya merasa aman dengan menggunakan fitur *biometric*, akan tetapi tingkat kenyamanannya kurang karena memiliki tingkat kegagalan autentikasi yang tinggi.

## Time Spent for Login

*Metric* yang menunjukkan rata-rata waktu yang dibutuhkan pengguna untuk *login*
 ke dalam aplikasi.

## Retention Rate

*Metric* ini menunjukkan jumlah pengguna yang kembali menggunakan aplikasi setelah jangka waktu tertentu.

**Perhitungan:** Total # *active users* bulanan / Total # instal bulanan

*Metric* ini tidak secara langsung mengukur keamanan dari fitur *biometric* yang dikembangkan. Akan tetapi, ketika *retention rate* tinggi dan di saat yang sama *biometric usage rate* tinggi dapat mengindikasikan bahwa pengguna merasa aman dengan adanya fitur tersebut.

<!-- # Conclusion
Sejumlah *metrics* yang diusulkan apabila digabungkan satu sama lain dapat memberikan gambaran kualitas dari -->

Image: [rawpixel.com](https://www.freepik.com/free-photo/biometric-technology-background-with-fingerprint-scanning-system-virtual-screen-digital-remix_15606690.htm#query=biometric%20fingerprint&position=1&from_view=keyword)
