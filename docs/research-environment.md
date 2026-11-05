# Research Environment

## 1. Overview

Dokumentasi ini menjelaskan lingkungan perangkat keras, perangkat lunak, dan jaringan yang digunakan dalam penelitian perbandingan arsitektur Microservices dan Monolithic N-Tier pada aplikasi e-commerce.

Implementasi Microservices menggunakan repository [dotnet/eShop](https://github.com/dotnet/eShop), sedangkan implementasi Monolithic N-Tier akan dikembangkan secara terpisah dengan teknologi yang disesuaikan agar perbandingan dapat dilakukan menggunakan lingkungan pengujian yang setara.

## 2. Hardware Environment

Penelitian menggunakan dua perangkat dengan peran berbeda.

| Perangkat | Peran |
|---|---|
| PC Laboratorium | Menjalankan aplikasi, database, dan infrastruktur pendukung |
| Laptop | Menjalankan Apache JMeter sebagai load testing client |

### 2.1 Application Server

| Komponen | Spesifikasi |
|---|---|
| Hostname | alfaa-S1200SP |
| Operating System | Ubuntu 22.04 |
| CPU | 4 Core |
| RAM | 30.94 GiB |
| Application Host | PC Laboratorium |

### 2.2 Testing Client

| Komponen | Keterangan |
|---|---|
| Operating System | Windows |
| Hostname | alfaaaaa |
| Testing Tool | Apache JMeter |
| Peran | Mengirimkan request dan mengumpulkan hasil pengujian |

*Spesifikasi detail laptop akan dilengkapi setelah inventarisasi perangkat.*

## 3. Software Environment

Perangkat lunak yang digunakan pada application server:

| Software | Version |
|---|---|
| Ubuntu | 22.04 |
| .NET SDK | 10.0.302 |
| .NET Aspire CLI | 13.6.4 |
| Docker Engine | 29.9.1 |
| Docker Compose | 5.6.0 |
| Tailscale | 1.104.5 |
| Git | 2.34.1 |

Versi Apache JMeter pada testing client akan dicatat setelah instalasi dan konfigurasi selesai.

## 4. Application Environment

### 4.1 Microservices Implementation

Implementasi Microservices menggunakan repository eShop dari Microsoft:

- Repository upstream: https://github.com/dotnet/eShop
- Research fork: https://github.com/alfaamarss/eshop-microservices-research
- Framework: .NET 10
- Application orchestration: .NET Aspire
- Containerization: Docker
- Frontend: Blazor
- Database: PostgreSQL dan database pendukung lainnya sesuai konfigurasi eShop.

Aplikasi dijalankan melalui .NET Aspire menggunakan AppHost pada:

`src/eShop.AppHost/eShop.AppHost.csproj`

### 4.2 Application Endpoint

| Parameter | Value |
|---|---|
| Application | eShop WebApp |
| Protocol | HTTP |
| Application Port | 5045 |
| Server Endpoint | 127.0.0.1:5045 |
| Client Endpoint | localhost:5045 |

Endpoint client diakses melalui SSH local port forwarding.

## 5. Network Environment

Komunikasi antara laptop dan PC laboratorium menggunakan Tailscale sebagai jaringan privat.

| Perangkat | Tailscale IP |
|---|---|
| PC Laboratorium | 100.86.118.35 |
| Laptop | 100.108.113.67 |

### 5.1 SSH Port Forwarding

SSH local port forwarding digunakan untuk meneruskan koneksi dari laptop menuju aplikasi yang berjalan pada PC laboratorium.

Perintah yang digunakan pada Windows PowerShell:

```powershell
ssh -L 5045:127.0.0.1:5045 alfaa@100.86.118.35
```

Setelah koneksi SSH berhasil dibuat, aplikasi dapat diakses dari laptop melalui:

`http://localhost:5045`

Dengan konfigurasi tersebut, request dari Apache JMeter pada laptop diteruskan melalui SSH dan Tailscale menuju aplikasi pada PC laboratorium.

## 6. Application Startup

Aplikasi eShop dijalankan pada PC laboratorium melalui terminal Ubuntu:

```bash
cd ~/eshopMicroservices-clean

ESHOP_USE_HTTP_ENDPOINTS=1 aspire run
```

Setelah seluruh resource berhasil berjalan, lakukan pemeriksaan endpoint:

```bash
curl -sS -o /dev/null \
-w "HTTP Status: %{http_code}\nTime: %{time_total}s\n" \
http://localhost:5045/
```

Respons HTTP 200 menunjukkan bahwa halaman utama aplikasi dapat diakses.

## 7. Research Environment Considerations

Untuk menjaga konsistensi pengujian antara implementasi Microservices dan Monolithic N-Tier, beberapa kondisi perlu disamakan:

1. Menggunakan PC laboratorium yang sama sebagai application server.
2. Menggunakan laptop yang sama sebagai testing client.
3. Menggunakan jalur komunikasi jaringan yang sama.
4. Menggunakan Apache JMeter dengan konfigurasi pengujian yang sama.
5. Menggunakan dataset dan skenario pengujian yang setara.
6. Melakukan pengujian secara bergantian agar kedua aplikasi tidak saling membebani sumber daya server.
7. Melakukan pengulangan pengujian sesuai rancangan metodologi penelitian.

## 8. Documentation Status

Dokumen ini merupakan catatan awal lingkungan penelitian. Informasi berikut akan dilengkapi setelah konfigurasi final ditetapkan:

- Spesifikasi detail laptop pengujian.
- Versi Apache JMeter.
- Konfigurasi database dan dataset pengujian.
- Konfigurasi final implementasi Monolithic N-Tier.
- Konfigurasi workload dan skenario pengujian.
- Hasil benchmark setelah pengujian dilaksanakan.
