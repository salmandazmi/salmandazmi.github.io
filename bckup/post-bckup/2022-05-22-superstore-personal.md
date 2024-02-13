---
layout: post
title:  "Superstore Sales Dashboard (Personal)"
author: salmandazmi
categories: [ Portofolio ]
tags: [ Binar Academy ]
image: assets/images/superstore-personal.png
comments: false
<!-- rating: 4.5 -->
---

*Superstore sales dashboard adalah proyek pengembangan dashboard untuk menganalisis performa penjualan perusahaan Superstore yang berfokus pada penyusunan business requirements document dan pembuatan dashboard. Proyek ini adalah bagian dari gold level project dari program bootcamp business intelligence analyst dari Binar Academy.*

**Authors**: Salman Azmi

**Tech Stack**: SQL, Google BigQuery, Google Data Studio

## Problem Definition
Superstore merupakan perusahaan penyedia peralatan kantor, termasuk furnitur dan perangkat teknologi, yang beroperasi di Amerika Serikat. Didirikan pada tahun 2002, Superstore telah memiliki cabang hampir di semua negara bagian dan menjadi salah satu perusahaan terbesar di industrinya. Perusahaan ini melayani pelanggan dari segmen consumer, corporate, maupun home office.

Pertumbuhan laba yang dicapai Superstore dalam empat tahun terakhir cenderung stagnan di angka 13% per tahun. Situasi ini tidak sebanding dengan pertumbuhan penjualan yang meningkat tajam dalam periode yang sama. Di samping itu, data tahun 2017 menunjukkan bahwa *profit margin* dari setiap region sangat bervariasi dengan nilai tertinggi 45% dan terendah -43%. Menanggapi hal tersebut, tim pemasaran menjalankan inisiatif bisnis berupa perubahan *pricing strategy* untuk meningkatkan *profit margin* perusahaan dengan fokus perbaikan di tiap region.

Sebagai salah satu fokus utama perusahaan ke depan, ketercapaian inisiatif bisnis ini sangat penting. Oleh karena itu, *dashboard* dibutuhkan untuk mengidentifikasi performa penjualan (termasuk laba dan *profit margin*) dengan cepat sehingga dapat direspons manajer regional maupun tim pemasaran dengan keputusan yang tepat.

Selengkapnya dapat dilihat dalam Business Requirements Document berikut: [`superstore-personal-brd.pdf`]('superstore-personal-brd.pdf')

## Data Collection

Dataset yang digunakan dalam project ini telah disediakan oleh Binar dan berisi 10K+ records dari tahun 2014-2018. Terdapat tiga tabel, yaitu Order, Customer, dan Categories.

## Data Loading and Cleansing
