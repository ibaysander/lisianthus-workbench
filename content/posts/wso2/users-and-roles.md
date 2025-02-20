---
date: '2025-02-18T11:04:26+07:00'
draft: false
title: 'Set Up Custom Roles dan Users pada WSO2 API Manager'
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
WSO2 API Manager menyediakan sistem manajemen pengguna dan peran yang komprehensif untuk mengatur akses ke berbagai fitur platform. Dalam tutorial ini, kita akan membahas cara membuat dan mengonfigurasi users dan roles di WSO2 API Manager 4.3.0.

## Mengapa Perlu Mengatur Users dan Roles?

Bayangkan di sebuah restoran pizza yang bernama PizzaShack. Di restoran ini, terdapat beberapa karyawan dengan tanggung jawab yang berbeda-beda. Ada karyawan yang bertugas membuat menu baru, dan ada juga yang bertugas mempublikasikan menu tersebut ke website restoran. Selain itu, ada pelanggan yang perlu mengakses menu dan melakukan pemesanan melalui website.

Situasi ini dapat dianalogikan sebagai:
- Pembuat menu → API Creator yang membuat dan mendefinisikan API
- Pengelola website → API Publisher yang mempublikasikan API ke portal
- Pelanggan → API Subscriber yang menggunakan API yang telah dipublikasikan

Tanpa sistem roles yang terstruktur, pengelolaan akses untuk setiap kelompok pengguna ini akan menjadi sangat rumit. Administrator sistem harus mengatur setiap izin secara manual untuk setiap pengguna baru, memastikan konsistensi izin di antara pengguna dengan tanggung jawab yang sama, dan melakukan perubahan satu per satu ketika ada pembaruan kebijakan akses.

Implementasi custom roles memberikan solusi yang lebih efisien untuk manajemen akses. Dengan membuat peran khusus untuk creator, publisher, dan subscriber, administrator dapat dengan mudah mengelompokkan izin berdasarkan tanggung jawab pekerjaan. Ketika ada staf baru yang bergabung, cukup dengan memberikan role yang sesuai, mereka langsung mendapatkan semua izin yang diperlukan untuk melakukan pekerjaan mereka.

Dalam tutorial ini, kita akan membuat tiga jenis roles utama:
1. API Creator (apicreator) - untuk karyawan yang membuat dan mengelola API
2. API Publisher (apipublisher) - untuk karyawan yang mempublikasikan API
3. Subscriber - untuk pelanggan yang menggunakan API

## Prasyarat

Sebelum memulai, pastikan Anda telah memenuhi persyaratan berikut:

1. WSO2 API Manager 4.3.0 telah terinstal dan berjalan dengan baik
2. Akses ke konsol admin WSO2 API Manager
3. Akun administrator (default: admin/admin)

## Langkah 1: Membuat Role Baru

1. Buka Command Prompt dan jalankan WSO2 API Manager:
```bash
cd <API-M_HOME>/bin
api-manager.bat
```

2. Akses Carbon Console di `https://localhost:9443/carbon` dan masuk menggunakan kredensial admin.

3. Untuk membuat role API Creator:
- Navigasi ke **Main** > **Users and Roles** > **Add** > **Add New Role**
- Masukkan `apicreator` sebagai nama role
- Klik **Next**
- Pilih izin berikut:
  * Configure > Governance (semua izin di bawahnya)
  * Login
  * Manage > API > Create
  * Manage > Resources > Govern (semua izin di bawahnya)
- Klik **Finish**

4. Untuk membuat role API Publisher:
- Ulangi langkah pembuatan role
- Masukkan `apipublisher` sebagai nama role
- Pilih izin berikut:
  * Login
  * Manage > API > Publish
- Klik **Finish**

## Langkah 2: Mengatur Role Mapping

WSO2 API Manager 4.3.0 menggunakan REST API dengan kontrol akses berbasis OAuth2 scopes. Setiap aksi di portal memerlukan scope tertentu yang terpetakan ke role Internal/*.

1. Masuk ke API Manager Admin Portal (`https://localhost:9443/admin`)
2. Navigasi ke **Settings** > **Scope Assignments**
3. Klik **Add scope mappings**
4. Untuk mapping API Creator:
   - Role name: `apicreator`
   - Mapping role: `Internal/creator`
   - Klik **Save**
5. Untuk mapping API Publisher:
   - Role name: `apipublisher`
   - Mapping role: `Internal/publisher`
   - Klik **Save**

## Langkah 3: Membuat User Baru

Setelah role terbuat, kita dapat membuat user dan menugaskan role yang sesuai.

1. Di Carbon Console, navigasi ke **Main** > **Users and Roles** > **Add** > **Add New User**

2. Untuk membuat user Creator:
   - Username: `creator`
   - Password: `password`
   - Klik **Next**
   - Pilih role `apicreator`
   - Klik **Finish**

3. Untuk membuat user Publisher:
   - Username: `publisher`
   - Password: `password`
   - Klik **Next**
   - Pilih role `apipublisher`
   - Klik **Finish**

4. Untuk membuat user Subscriber:
   - Username: `subscriber`
   - Password: `password`
   - Klik **Next**
   - Pilih role `Internal/subscriber`
   - Klik **Finish**

5. Untuk membuat user tanpa role:
   - Username: `mike`
   - Password: `password`
   - Klik **Finish**

## Verifikasi Konfigurasi

Untuk memastikan konfigurasi berjalan dengan baik:

1. Masuk ke Publisher Portal (`https://localhost:9443/publisher`) menggunakan akun creator
   - Verifikasi bahwa user dapat membuat API baru
   - Pastikan tidak ada akses ke fitur publikasi

2. Masuk menggunakan akun publisher
   - Verifikasi bahwa user dapat mempublikasikan API
   - Pastikan tidak ada akses ke fitur pembuatan API

3. Masuk ke Developer Portal (`https://localhost:9443/devportal`) menggunakan akun subscriber
   - Verifikasi bahwa user dapat melihat dan berlangganan ke API yang dipublikasikan

## Kesimpulan

Dengan mengikuti tutorial ini, Anda telah berhasil membuat dan mengonfigurasi sistem users dan roles di WSO2 API Manager 4.3.0. Sistem ini memungkinkan Anda untuk:
- Membuat role khusus untuk creator dan publisher
- Mengatur mapping role dengan OAuth2 scopes
- Membuat user dengan role yang sesuai
- Memverifikasi bahwa setiap user memiliki akses yang tepat sesuai rolenya

Selamat mencoba!