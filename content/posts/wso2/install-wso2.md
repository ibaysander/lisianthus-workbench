---
date: '2025-02-18T06:05:43+07:00'
draft: false
title: 'Tutorial Instalasi WSO2 API Manager di Windows 11'
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
![wso2img](/wso2/wso2.png)
WSO2 API Manager adalah platform open-source yang digunakan untuk mengelola, mengatur, dan memantau penggunaan API. Dalam tutorial ini, kita akan membahas langkah-langkah instalasi WSO2 API Manager versi 4.4.0 di Windows 11 menggunakan JDK 21.

## Prasyarat Instalasi

Sebelum memulai instalasi, pastikan sistem Anda memenuhi persyaratan berikut:

1. **Sistem Operasi**
   - Windows 11 (64-bit)
   - Pastikan sistem dalam keadaan terupdate

2. **Java Development Kit (JDK)**
   - Pilih salah satu versi berikut:
     * JDK 11
     * JDK 17
     * JDK 21
   - Unduh dari situs resmi:
     * [Oracle](https://www.oracle.com/java/technologies/downloads/)
     * [OpenJDK](https://openjdk.org/projects/jdk/21/)

3. **Sumber Daya Sistem**
   - RAM: Minimal 4 GB (2 GB untuk JVM dan 2 GB untuk Sistem Operasi)
   - Ruang Disk: Minimal 10 GB
   - Pastikan sistem memiliki spesifikasi yang cukup untuk multitasking

4. **Konfigurasi Port**
   - Pastikan port-port berikut tersedia:
     * 9443 (HTTPS)
     * 8280 (HTTP)
     * 8243 (API)
   - Jika port sudah digunakan, siapkan untuk konfigurasi ulang

## Langkah 1: Unduh WSO2 API Manager

1. Akses [GitHub WSO2 API Manager](https://github.com/wso2/product-apim)
2. Navigasi ke bagian "Releases"
3. Unduh file `wso2am-4.4.0.zip`
4. Ekstrak file ke direktori yang diinginkan, misalnya:
   ```bash
   D:\wso2am-4.4.0
   ```

## Langkah 2: Instalasi JDK

1. Unduh JDK yang dipilih (11, 17, atau 21)
2. Jalankan file installer JDK
3. Ikuti wizard instalasi hingga selesai
4. Konfigurasi environment variable:
   - Buka System Properties > Advanced > Environment Variables
   - Tambahkan JAVA_HOME:
     * Variable Name: `JAVA_HOME`
     * Variable Value: `C:\Program Files\Java\jdk-21`
   - Tambahkan path ke PATH:
     * `%JAVA_HOME%\bin`
5. Verifikasi instalasi:
   ```bash
   java --version
   ```

## Langkah 3: Konfigurasi WSO2 API Manager

1. Navigasi ke direktori instalasi:
    ```bash
    cd D:\wso2am-4.4.0\repository\conf
    ```
2. Edit file `deployment.toml`:
   - Konfigurasi port (jika diperlukan):
     ```toml
     [server]
     offset = 1
     ```
   - Konfigurasi akun admin (opsional):
     ```toml
     [super_admin]
     username = "admin"
     password = "admin"
     ```

## Langkah 4: Menjalankan WSO2 API Manager

1. Buka Command Prompt sebagai Administrator
2. Navigasi ke direktori bin:
    ```bash
    cd D:\wso2am-4.4.0\bin
    ```
3. Jalankan server:
    ```bash
    api-manager.bat
    ```
4. Tunggu hingga proses startup selesai
5. Periksa log untuk memastikan server berjalan
   ![server running](/wso2/startup.png)

## Langkah 5: Mengakses Konsol WSO2 API Manager

1. Buka browser dan akses portal berikut:
   - **Publisher**: `https://localhost:9443/publisher`
   - **Developer Portal**: `https://localhost:9443/devportal`
   - **Admin Console**: `https://localhost:9443/admin`
   - **Carbon Console**: `https://localhost:9443/carbon`

2. Login dengan kredensial default:
   - Username: `admin`
   - Password: `admin`

## Langkah 6: Keamanan Dasar

1. Ganti Password Admin:
   - Akses Carbon Console
   - Navigasi ke Main > Identity > Users and Roles > List > Users
   - Pilih user `admin`
   - Klik "Change Password"
   - Masukkan password baru yang kuat

2. Verifikasi Akses:
   - Pastikan semua portal dapat diakses
   - Uji fitur dasar di setiap portal
   - Dokumentasikan kredensial baru

## Langkah 7: Pemeliharaan

1. Menghentikan Server:
   - Tekan CTRL+C di Command Prompt yang menjalankan server
   - Tunggu proses shutdown selesai

2. Backup:
   - Buat cadangan file logs secara berkala
   - Simpan konfigurasi penting
   - Dokumentasikan perubahan yang dilakukan

## Kesimpulan

Dengan mengikuti langkah-langkah di atas, Anda telah berhasil menginstal WSO2 API Manager 4.4.0 di Windows 11 menggunakan JDK 21. Pastikan untuk melakukan konfigurasi keamanan dasar sebelum menggunakan produksi.

Untuk bantuan lebih lanjut, Anda dapat mengakses dokumentasi resmi WSO2 atau forum komunitas.