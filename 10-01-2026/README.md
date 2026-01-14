# Catatan Harianku

###### 10 Januari 2026

## PENTING UNTUK PEMULA BELAJAR PROXMOX

Catatan ini saya tulis berdasarkan pengalaman pribadi ketika mengulik **jaringan Proxmox**. Ide awalnya juga terinspirasi dari AI, dan tujuan utama dokumentasi ini adalah agar **saya sendiri di masa depan** (dan teman-teman yang membaca) tidak mengulangi kesalahan yang sama.

Saya sering melakukan eksperimen jaringan (uji lab sendiri). Di sinilah letak bahayanya: ketika kita **belum benar-benar paham jaringan**, lalu mulai mengatur IP, gateway, dan interface secara manual (sering dibantu AI), ada kemungkinan **kehilangan akses internet sepenuhnya** dan tidak tahu jalan keluarnya.

> ⚠️ **Pesan penting:** Dokumentasi ini dibuat sebagai *pegangan darurat* ketika Proxmox mengalami masalah jaringan.

---

## Prinsip Dasar yang Wajib Diingat

### 1. Gunakan **LAN**, Jangan WiFi (Untuk Full Server)

❌ **Jangan pernah** mengandalkan WiFi jika:

* Proxmox diinstal langsung di **1 PC khusus server**
* Digunakan sebagai **home server / production server**

✅ **Gunakan kabel LAN (WAJIB)**

Alasannya sederhana:

* Server pada umumnya **memang didesain menggunakan LAN**
* Hampir semua tutorial Proxmox **berujung ke LAN**
* WiFi tidak stabil untuk server jangka panjang

> **Ingat:** `#Gunakan kabel LAN, jangan WiFi untuk full server`

### 2. VirtualBox = Boleh WiFi (Hanya untuk Belajar)

Jika kamu:

* Menggunakan **VirtualBox**
* Tujuan hanya **belajar / eksperimen**

👉 WiFi **tidak masalah**.

Namun menurut saya pribadi:

* Install Proxmox di VirtualBox **kurang realistis**
* Tidak cocok untuk production
* Lebih baik dijadikan **sekadar bahan belajar konsep**

---

## Setup Jaringan Proxmox (Studi Kasus Nyata)

### Kondisi Studi Kasus

Kasus ini sering terjadi ketika:

* Kabel LAN **tidak sengaja tercabut**
* Listrik **padam**
* Server hidup kembali tapi **tidak bisa diakses**

> ❗ Jangan panik. Jika IP diset **statis**, biasanya aman.

---

## 1. Cek Konfigurasi Network Proxmox

Masuk sebagai `root`, lalu buka file konfigurasi jaringan:

```bash
nano /etc/network/interfaces
```

Contoh isi konfigurasi:

```bash
auto lo
iface lo inet loopback

iface nic0 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.0.150/24   # Sesuaikan dengan IP router
        gateway 192.168.0.1        # Gateway router
        bridge-ports nic0           # Port LAN (bisa nic0 / eno1 / eth0)
        bridge-stp off
        bridge-fd 0

iface nic1 inet manual
iface nic2 inet manual

source /etc/network/interfaces.d/*
```

---

## 2. Cek Status Interface Jaringan

Gunakan perintah:

```bash
ip addr
```

Contoh hasil normal:

```text
nic0   -> [UP]
vmbr0  -> [UP]
wlp4s0 -> [DOWN]
```

### Cara Membacanya

* Cari interface **LAN / Ethernet**
* Perhatikan status:

  * 🟩 **UP** → aktif / hidup
  * 🟥 **DOWN** → mati / tidak terhubung

Interface dengan status **UP** adalah yang sedang aktif.

---

## 3. Simulasi Masalah (Kabel Dicabut)

Jika kabel LAN dicabut lalu dipasang kembali, biasanya hasilnya menjadi:

```text
nic0   -> [UP]
vmbr0  -> [UP]
wlp4s0 -> [DOWN]
```

Ini artinya:

* Proxmox **kehilangan koneksi jaringan**
* Web UI **tidak bisa diakses**

---

## 4. Menghidupkan Interface Secara Manual

### a. Tentukan Interface LAN

Biasanya bernama:

```text
nic0   -> [UP]
vmbr0  -> [UP]
wlp4s0 -> [DOWN]
```

### b. Aktifkan Interface LAN

```bash
ip link set nic0 up
systemctl restart networking
```

> Ganti `nic0` sesuai nama interface milikmu.

---

## 5. Cek Ulang Status Jaringan

```bash
ip addr
```

Target akhir:

* Interface LAN → 🟩 **UP**
* `vmbr0` → 🟩 **UP**

Jika **keduanya UP**, maka:

* Proxmox bisa diakses via browser
* Masalah jaringan dasar selesai 🎉

---

## Penutup

Jika kamu sudah sampai tahap ini dan semua interface **UP**, berarti:

* Masalah teknis dasar jaringan **sudah kamu kuasai**
* Kamu bisa lanjut konfigurasi VM, Container, dan service lain lewat Web UI

> 📌 Simpan catatan ini baik-baik. Saat panik, dokumentasi sendiri adalah penyelamat terbaik.
