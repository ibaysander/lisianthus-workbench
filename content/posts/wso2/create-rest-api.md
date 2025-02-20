---
date: '2025-02-18T11:52:38+07:00'
draft: false
title: 'Tutorial Membuat dan Mempublikasikan API di WSO2 API Manager 4.3.0'
tags: ["API Management", "WSO2", "Microservices", "Integration"]
categories: ["Technology", "Tutorial"]
author: "Walawe"
description: "Lorem Ipsum"
showToc: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
---

Setelah berhasil mengatur pengguna dan peran di WSO2 API Manager, langkah selanjutnya adalah membuat dan mempublikasikan API. Dalam tutorial ini, kita akan menggunakan contoh kasus PizzaShack untuk memahami proses pembuatan API secara menyeluruh.

Bayangkan Anda adalah pengembang di PizzaShack yang ditugaskan untuk membuat API yang akan memungkinkan pelanggan memesan pizza secara online. API ini perlu memiliki fungsi untuk melihat menu, membuat pesanan, dan mengelola pesanan yang ada.

Mari kita pelajari bagaimana mewujudkan kebutuhan ini menggunakan WSO2 API Manager.

---

## **Prasyarat**
Sebelum memulai, pastikan Anda telah memiliki:
1. WSO2 API Manager 4.3.0 yang telah terinstal dan berjalan
2. Akun dengan role creator untuk membuat API
3. Akun dengan role publisher untuk mempublikasikan API
4. Pemahaman dasar tentang REST API dan HTTP methods

---

## **Langkah 1: Membuat API Baru**

Pertama, kita akan masuk sebagai creator untuk membuat API. Pada proses ini kita perlu mendefinisikan semua layanan yang akan tersedia untuk pelanggan.

1. Buka Publisher Portal di `https://localhost:9443/publisher`
2. Masuk menggunakan akun creator yang telah dibuat sebelumnya
3. Di panel navigasi kiri, pilih **APIs**
4. Pilih **Start From Scratch** di bawah bagian REST APIs
5. Isi informasi dasar API:
   ```
   Name: PizzaShack
   Context: /pizzashack
   Version: 1.0.0
   Endpoint: https://localhost:9443/am/sample/pizzashack/v1/api/
   ```

Perhatikan bahwa 'Context' adalah seperti alamat restoran - ini adalah path dasar yang akan digunakan pelanggan untuk mengakses API kita. 'Version' memungkinkan kita untuk mengembangkan API tanpa merusak aplikasi yang sudah menggunakannya.

---

## **Langkah 2: Mengonfigurasi Portal dan Runtime**

Setelah membuat kerangka dasar API, kita perlu mengatur bagaimana API ini akan terlihat dan beroperasi.

### Konfigurasi Portal

1. Buka tab **Basic Info** di bawah Portal Configurations
2. Atur konfigurasi berikut:
   ```
   Publisher Access Control: All
   Developer Portal Visibility: Public
   Description: PizzaShackAPI: Allows to manage pizza orders (create, update, retrieve orders)
   Tags: pizza, order, pizza-menu
   ```

Pengaturan visibility sebagai "Public" adalah seperti membuat restoran Anda terlihat di Google Maps - ini memastikan pelanggan dapat menemukan layanan Anda.

### Konfigurasi Runtime

1. Buka tab **Runtime** di bawah API Configurations
2. Atur keamanan dan performa API:
   ```
   Transport Level Security: HTTP/HTTPS
   Application Level Security: OAuth2
   CORS Configuration: Disabled
   Response Caching: Disabled
   Maximum Backend Throughput: Unlimited
   ```

Keamanan di sini seperti sistem keamanan restoran - OAuth2 memastikan hanya pelanggan yang terverifikasi yang dapat menggunakan layanan.

---

## **Langkah 3: Mendefinisikan Resources**

Resources adalah layanan spesifik yang API kita sediakan. Ini seperti menu restoran yang mendefinisikan apa yang bisa dipesan pelanggan.

1. Buka tab **Resources**
2. Tambahkan resource berikut:
   ```
   GET /menu - Untuk melihat menu pizza
   POST /order - Untuk membuat pesanan baru
   GET /order/{orderid} - Untuk melihat status pesanan
   PUT /order/{orderid} - Untuk mengubah pesanan
   ```

Mari kita tambahkan juga scope untuk mengontrol akses:

1. Buka tab **Local Scopes**
2. Tambahkan scope baru:
   ```
   Name: order_pizza
   Display Name: Order Pizza
   Description: Only users with admin role and internal/subscriber role can order
   Roles: admin, Internal/subscriber
   ```

3. Kembali ke tab Resources dan tetapkan scope `order_pizza` ke resource POST /order

---

## **Langkah 4: Menambahkan Dokumentasi**

Dokumentasi yang baik adalah kunci kesuksesan API. Ini seperti buku petunjuk yang membantu pelanggan memahami cara menggunakan layanan kita.

1. Buka tab **Documents** di bawah Portal Configurations
2. Klik **Add New Document**
3. Isi informasi dokumen:
   ```
   Name: PizzaShack
   Summary: This is the official documentation for the PizzaShack API
   Type: How To
   Source: Inline
   ```

---

## **Langkah 5: Deploy dan Publish API**

Sekarang API kita siap untuk dipublikasikan. Proses ini seperti membuka restoran untuk umum.

1. Buka tab **Deployments** di bawah Deploy
2. Pilih Gateway yang diinginkan dan klik Deploy
3. Logout dari akun creator
4. Login menggunakan akun publisher
5. Buka API PizzaShack
6. Di tab **Lifecycle**, klik tombol Publish

---

## **Verifikasi API**

Untuk memastikan API berfungsi dengan baik:

1. Buka Developer Portal di `https://localhost:9443/devportal`
2. Login menggunakan akun subscriber
3. Cari API PizzaShack
4. Verifikasi bahwa Anda dapat:
   - Melihat dokumentasi API
   - Melihat semua endpoint yang tersedia
   - Berlangganan ke API

---

## **Kesimpulan**

Melalui tutorial ini, kita telah berhasil membuat dan mempublikasikan API PizzaShack yang memungkinkan pelanggan untuk melihat menu dan melakukan pemesanan. Proses ini menunjukkan bagaimana WSO2 API Manager memfasilitasi seluruh siklus hidup API, dari pembuatan hingga publikasi.

Sekarang API sudah siap digunakan oleh developer lain untuk mengintegrasikan layanan pemesanan pizza ke dalam aplikasi mereka. Dengan dokumentasi yang lengkap dan keamanan yang terkonfigurasi dengan baik, API ini siap untuk digunakan dalam lingkungan produksi.

Selamat mencoba!