# 🌐 Perancangan dan Implementasi Jaringan Terpadu Yayasan ARA

| Nama | NRP |
|------|-----|
| Christiano Ronaldo Silalahi | 5027241025 |
| Syifa Nurul Alfiah| 5027241019 |
| Nisrina bilqis| 5027241054 |
| Muhammad Ardiansyah Tri| 5027241091 |


Proyek jaringan komputer ini merancang infrastruktur jaringan lengkap untuk Yayasan ARA yang mencakup dua gedung utama dan satu kantor cabang dengan implementasi VLSM, CIDR, routing dinamis, dan teknologi keamanan modern.

## 📋 Daftar Isi
- [Gambaran Umum](#gambaran-umum)
- [Topologi Jaringan](#topologi-jaringan)
- [Skema Pengalamatan IP](#skema-pengalamatan-ip)
- [Konfigurasi Routing](#konfigurasi-routing)
- [Fitur Keamanan](#fitur-keamanan)

---

## 🎯 Gambaran Umum

### Tujuan Proyek
Merancang dan mengimplementasikan jaringan terpadu yang menghubungkan:
- **Gedung Utama Yayasan ARA** - 6 unit kerja (VLAN 11-16)
- **Gedung ARA Tech** - 5 lantai dengan 14 departemen (VLAN 101-105)
- **Kantor Cabang** - 1 regional office (VLAN 201)

### Teknologi yang Digunakan
- **Topologi**: Hierarchical Three-Layer (Core, Distribution, Access)
- **Subnetting**: VLSM untuk Gedung Utama, CIDR untuk Gedung ARA Tech
- **Routing**: Static Routing + OSPF (Dynamic)
- **NAT**: PAT/NAT Overload untuk akses internet
- **VPN**: GRE Tunnel untuk koneksi Gedung Utama - Kantor Cabang
- **DHCP**: Automatic IP assignment untuk end devices
- **VLAN**: Segmentasi jaringan per departemen/lantai

---

## 🗺️ Topologi Jaringan

### Arsitektur Hierarki
<img width="1654" height="742" alt="image" src="https://github.com/user-attachments/assets/29c5359f-2607-48ce-99c4-93f829cf1fda" />


### Perangkat Utama
| Perangkat | Model | Fungsi |
|-----------|-------|--------|
| Router A1 | Cisco 4331 | Core Router Gedung Utama + Edge Router |
| Router A3 | Cisco 4331 | Core Router Gedung ARA Tech |
| Router A9 | Cisco 4331 | Router Kantor Cabang |
| Router A2, A8, A10 | Cisco 2911 | WAN Link Routers (Point-to-Point) |
| Switch A11-A15 | Cisco 3750 | Distribution - Gedung Utama |
| Switch A4-A7 | Cisco 3750 | Distribution - ARA Tech (per Lantai) |
| Switch Access | Cisco 2960 | Access Layer untuk end devices |

---

## 📊 Skema Pengalamatan IP

### 1. Gedung Utama (VLSM) - 10.0.0.0/21

Menggunakan **Variable Length Subnet Masking** untuk efisiensi IP.

| No | Departemen | Jumlah Host | Host Tersedia | Prefix | Network ID | VLAN ID | Switch |
|----|-----------|-------------|---------------|--------|-----------|---------|--------|
| A1 | Layanan Operasional | 380 | 510 | /23 | 10.0.0.0 | VLAN 11 | A11 |
| A11 | Kurikulum & Penjaminan Mutu | 220 | 254 | /24 | 10.0.2.0 | VLAN 12 | A11 |
| A12 | SDM Pendidikan | 95 | 126 | /25 | 10.0.3.0 | VLAN 13 | A12 |
| A13 | Sarana Prasarana | 45 | 62 | /26 | 10.0.3.128 | VLAN 14 | A13 |
| A14 | Pembinaan & Pengawasan | 18 | 30 | /27 | 10.0.3.192 | VLAN 15 | A14 |
| A15 | IT Pendidikan (Server) | 6 | 14 | /28 | 10.0.3.224 | VLAN 16 | A15 |

#### Detail Subnet Gedung Utama

| Departemen | Network ID | Subnet Mask | Broadcast | Range IP Host | Gateway |
|-----------|-----------|-------------|-----------|---------------|---------|
| Layanan Operasional | 10.0.0.0 | 255.255.254.0 | 10.0.1.255 | 10.0.0.1 - 10.0.1.254 | 10.0.0.1 |
| Kurikulum & Penjaminan Mutu | 10.0.2.0 | 255.255.255.0 | 10.0.2.255 | 10.0.2.1 - 10.0.2.254 | 10.0.2.1 |
| SDM Pendidikan | 10.0.3.0 | 255.255.255.128 | 10.0.3.127 | 10.0.3.1 - 10.0.3.126 | 10.0.3.1 |
| Sarana Prasarana | 10.0.3.128 | 255.255.255.192 | 10.0.3.191 | 10.0.3.129 - 10.0.3.190 | 10.0.3.129 |
| Pembinaan & Pengawasan | 10.0.3.192 | 255.255.255.224 | 10.0.3.223 | 10.0.3.193 - 10.0.3.222 | 10.0.3.193 |
| IT Pendidikan | 10.0.3.224 | 255.255.255.240 | 10.0.3.239 | 10.0.3.225 - 10.0.3.238 | 10.0.3.225 |

---

### 2. Gedung ARA Tech (CIDR) - 10.0.4.0/22

Menggunakan **CIDR** dengan agregasi per lantai.

| No | Lantai/Departemen | Total Host | Host Tersedia | Prefix | Network ID | VLAN ID | Switch |
|----|------------------|------------|---------------|--------|-----------|---------|--------|
| A3 | ARA Tech - Lantai 1 | 96 | 126 | /25 | 10.0.4.0 | VLAN 101 | A3 |
| A6 | ARA Tech - Lantai 4 | 105 | 126 | /25 | 10.0.4.128 | VLAN 104 | A6 |
| A4 | ARA Tech - Lantai 2 | 102 | 126 | /25 | 10.0.5.0 | VLAN 102 | A4 |
| A5 | ARA Tech - Lantai 3 | 88 | 126 | /25 | 10.0.5.128 | VLAN 103 | A5 |
| A7 | ARA Tech - Lantai 5 | 46 | 62 | /26 | 10.0.6.0 | VLAN 105 | A7 |

#### Detail Per Departemen

**Lantai 1 (VLAN 101) - 10.0.4.0/25**
| Departemen | Host Asli | Host+Cadangan | Prefix | Network ID | Broadcast | Range IP Host |
|-----------|-----------|---------------|--------|-----------|-----------|---------------|
| IT Support | 45 | 54 | /26 | 10.0.4.0 | 10.0.4.63 | 10.0.4.2 - 10.0.4.62 |
| Server & Data Center | 12 | 15 | /28 | 10.0.4.64 | 10.0.4.79 | 10.0.4.66 - 10.0.4.78 |
| Cybersecurity | 22 | 27 | /27 | 10.0.4.80 | 10.0.4.111 | 10.0.4.82 - 10.0.4.110 |

**Lantai 2 (VLAN 102) - 10.0.5.0/25**
| Departemen | Host Asli | Host+Cadangan | Prefix | Network ID | Broadcast | Range IP Host |
|-----------|-----------|---------------|--------|-----------|-----------|---------------|
| Marketing | 35 | 42 | /26 | 10.0.5.0 | 10.0.5.63 | 10.0.5.2 - 10.0.5.62 |
| Sales | 25 | 30 | /27 | 10.0.5.64 | 10.0.5.95 | 10.0.5.66 - 10.0.5.94 |
| Human Resources | 25 | 30 | /27 | 10.0.5.96 | 10.0.5.127 | 10.0.5.98 - 10.0.5.126 |

**Lantai 3 (VLAN 103) - 10.0.5.128/25**
| Departemen | Host Asli | Host+Cadangan | Prefix | Network ID | Broadcast | Range IP Host |
|-----------|-----------|---------------|--------|-----------|-----------|---------------|
| R&D | 55 | 62 | /26 | 10.0.5.128 | 10.0.5.191 | 10.0.5.130 - 10.0.5.190 |
| People Development | 18 | 22 | /27 | 10.0.5.192 | 10.0.5.223 | 10.0.5.194 - 10.0.5.222 |

**Lantai 4 (VLAN 104) - 10.0.4.128/25**
| Departemen | Host Asli | Host+Cadangan | Prefix | Network ID | Broadcast | Range IP Host |
|-----------|-----------|---------------|--------|-----------|-----------|---------------|
| Keuangan | 28 | 34 | /26 | 10.0.4.128 | 10.0.4.191 | 10.0.4.130 - 10.0.4.190 |
| Legal | 18 | 22 | /27 | 10.0.4.192 | 10.0.4.223 | 10.0.4.194 - 10.0.4.222 |
| Customer Service | 40 | 48 | /26 | 10.0.4.224 | 10.0.4.255 | 10.0.4.226 - 10.0.4.254 |

**Lantai 5 (VLAN 105) - 10.0.6.0/26**
| Departemen | Host Asli | Host+Cadangan | Prefix | Network ID | Broadcast | Range IP Host |
|-----------|-----------|---------------|--------|-----------|-----------|---------------|
| Auditorium | 15 | 18 | /27 | 10.0.6.0 | 10.0.6.31 | 10.0.6.2 - 10.0.6.30 |
| Executive Office | 12 | 15 | /28 | 10.0.6.32 | 10.0.6.47 | 10.0.6.34 - 10.0.6.46 |
| Guest Lounge | 10 | 12 | /28 | 10.0.6.48 | 10.0.6.63 | 10.0.6.50 - 10.0.6.62 |

---

### 3. Supernetting (Route Aggregation)

#### Agregasi Per Lantai Gedung ARA Tech

| No | Network Aggregation | Prefix | Range IP | Subnet Digabung | Penjelasan |
|----|-------------------|--------|----------|-----------------|-----------|
| 1 | 10.0.4.0/24 | /24 | 10.0.4.0 - 10.0.4.255 | 6 subnet | **Lantai 1 & 4**: Menyederhanakan routing infrastruktur (IT, Server, Cybersec) dan bisnis (Keuangan, Legal, CS) - dari 6 entri → 1 entri |
| 2 | 10.0.5.0/24 | /24 | 10.0.5.0 - 10.0.5.255 | 5 subnet | **Lantai 2 & 3**: Menggabungkan departemen produktif (Marketing, Sales, HR, R&D, People Dev) - dari 5 entri → 1 entri. Memudahkan QoS |
| 3 | 10.0.6.0/26 | /26 | 10.0.6.0 - 10.0.6.63 | 3 subnet | **Lantai 5**: Area eksekutif dan publik (Auditorium, Executive, Guest Lounge) dengan kebijakan keamanan ketat |

#### Super-Aggregation (Routing Eksternal)

| Super CIDR Block | Prefix | Range IP | Coverage | Manfaat Utama |
|-----------------|--------|----------|----------|---------------|
| **10.0.4.0/22** | /22 | 10.0.4.0 - 10.0.7.255 | **SELURUH Gedung ARA Tech** | Menggabungkan 14 subnet departemen menjadi **1 entry routing**. Meningkatkan performa lookup, stabilitas, dan menyisakan 448 alamat (10.0.6.64 - 10.0.7.255) untuk ekspansi |

---

### 4. Kantor Cabang & WAN Links

#### Kantor Cabang
| No | Lokasi | Total Host | Host Tersedia | Prefix | Network ID | VLAN ID | Switch |
|----|--------|------------|---------------|--------|-----------|---------|--------|
| A9 | Regional Office | 50 | 62 | /26 | 10.0.6.64 | VLAN 201 | A9 |

**Detail Subnet Cabang**:
- **Network ID**: 10.0.6.64
- **Subnet Mask**: 255.255.255.192 (/26)
- **Broadcast**: 10.0.6.127
- **Range IP Host**: 10.0.6.65 - 10.0.6.126
- **Gateway**: 10.0.6.65

#### WAN Links (Point-to-Point /30)
| Link | Router | Host | Prefix | Network ID | IP Address |
|------|--------|------|--------|-----------|-----------|
| Utama ↔ ARA Tech | A2 | 4 | /30 | 172.16.0.0 | 172.16.0.1 ↔ 172.16.0.2 |
| ARA Tech ↔ Cabang | A8 | 4 | /30 | 172.16.0.4 | 172.16.0.5 ↔ 172.16.0.6 |
| Cabang ↔ Utama (GRE) | A10 | 4 | /30 | 172.16.0.8 | 172.16.0.9 ↔ 172.16.0.10 |

---

## 🔀 Konfigurasi Routing

### 1. Static Routing (Internal)
**Implementasi**: Pada Core Router A3 (ARA Tech) untuk routing antar-lantai
- Menghubungkan VLAN 101-105
- Routing antar subnet 10.0.4.0/24, 10.0.5.0/24, 10.0.6.0/26

**Keuntungan**: Cepat, aman, overhead rendah, topologi stabil

```cisco
! Static Route Example (Router A3)
ip route 10.0.4.0 255.255.255.0 [next-hop-lantai-1-4]
ip route 10.0.5.0 255.255.255.0 [next-hop-lantai-2-3]
ip route 10.0.6.0 255.255.255.192 [next-hop-lantai-5]
```

### 2. Dynamic Routing (OSPF)

```
OSPF Area Configuration:
├── Area 0 (Backbone)
│   ├── Router A1 (Gedung Utama)
│   ├── Router A3 (ARA Tech)
│   └── WAN Links (A2, A8)
│
└── Area 1 (Regular)
    └── Router A9 (Kantor Cabang)
```

#### Route Advertisement

| Koneksi | Protokol | Area | Network yang Diiklankan |
|---------|----------|------|-------------------------|
| A1 ↔ A3 (Utama ↔ ARA Tech) | OSPF | Area 0 | 10.0.0.0/21 (Gedung Utama)<br>10.0.4.0/22 (ARA Tech) |
| A3 ↔ A9 (ARA Tech ↔ Cabang) | OSPF | Area 0/1 | 10.0.4.0/22 (ARA Tech)<br>10.0.6.64/26 (Cabang) |
| A1 ↔ A9 (Utama ↔ Cabang) | OSPF + GRE | Area 0/1 | 10.0.0.0/21 ↔ 10.0.6.64/26 |

```cisco
! OSPF Configuration Example (Router A1)
router ospf 1
 network 10.0.0.0 0.0.7.255 area 0
 network 172.16.0.0 0.0.0.3 area 0
 network 172.16.0.8 0.0.0.3 area 1

! OSPF Configuration (Router A3)
router ospf 1
 network 10.0.4.0 0.0.3.255 area 0
 network 172.16.0.0 0.0.0.3 area 0
 network 172.16.0.4 0.0.0.3 area 0
```

---

## 🔐 Fitur Keamanan

### 1. NAT Overload (PAT)
- **Lokasi**: Edge Router A1 (Gedung Utama)
- **IP Publik**: 203.0.113.1 (contoh)
- **IP Private yang Ditranslasi**:
  - Gedung Utama: 10.0.0.0/21
  - Gedung ARA Tech: 10.0.4.0/22
  - Kantor Cabang: 10.0.6.64/26
- **Fungsi**: Mengizinkan semua perangkat internal mengakses internet melalui 1 IP publik

```cisco
! NAT Overload Configuration
ip nat inside source list 1 interface GigabitEthernet0/0 overload

access-list 1 permit 10.0.0.0 0.0.7.255
access-list 1 permit 10.0.4.0 0.0.3.255
access-list 1 permit 10.0.6.64 0.0.0.63

interface GigabitEthernet0/0
 ip nat outside

interface GigabitEthernet0/1
 ip nat inside
```

### 2. GRE Tunnel (Gedung Utama ↔ Kantor Cabang)

**Arsitektur Tunnel**:
- **Source**: Router A1 (Interface WAN)
- **Destination**: Router A9 (Interface WAN)
- **Tunnel Network**: 172.16.0.8/30
  - A1 (Tunnel0): 172.16.0.9
  - A9 (Tunnel0): 172.16.0.10

```cisco
! GRE Tunnel Configuration (Router A1)
interface Tunnel0
 ip address 172.16.0.9 255.255.255.252
 tunnel source GigabitEthernet0/2
 tunnel destination [IP-Public-A9]
 tunnel mode gre ip

! GRE Tunnel Configuration (Router A9)
interface Tunnel0
 ip address 172.16.0.10 255.255.255.252
 tunnel source GigabitEthernet0/2
 tunnel destination [IP-Public-A1]
 tunnel mode gre ip
```

**Verifikasi**: 
```
<img width="986" height="111" alt="image" src="https://github.com/user-attachments/assets/c74584f7-5f0a-44f9-95ef-cb2ce5cc7c23" />

```

---

## 💻 Konfigurasi DHCP

### Strategi IP Assignment
- **IP Statis**: Router interfaces, Servers, Switch Management
- **DHCP Pool**: End devices (PC, laptop, printer, mobile)

### DHCP Configuration per Departemen

#### Gedung Utama
| Departemen | VLAN | IP Statis | Gateway | DHCP Pool |
|-----------|------|-----------|---------|-----------|
| Layanan Operasional | VLAN 11 | 10.0.0.2 - 10.0.0.10 | 10.0.0.1 | 10.0.0.11 - 10.0.1.254 |
| Kurikulum | VLAN 12 | 10.0.2.2 - 10.0.2.10 | 10.0.2.1 | 10.0.2.11 - 10.0.2.254 |
| SDM Pendidikan | VLAN 13 | 10.0.3.2 - 10.0.3.10 | 10.0.3.1 | 10.0.3.11 - 10.0.3.126 |

#### Gedung ARA Tech
| Lantai/VLAN | IP Statis | Gateway | DHCP Pool |
|------------|-----------|---------|-----------|
| Lantai 1 (VLAN 101) | 10.0.4.2 - 10.0.4.10 | 10.0.4.1 | 10.0.4.11 - 10.0.4.126 |
| Lantai 2 (VLAN 102) | 10.0.5.2 - 10.0.5.10 | 10.0.5.1 | 10.0.5.11 - 10.0.5.127 |
| Lantai 3 (VLAN 103) | 10.0.5.130 - 10.0.5.138 | 10.0.5.129 | 10.0.5.139 - 10.0.5.223 |
| Lantai 4 (VLAN 104) | 10.0.4.130 - 10.0.4.138 | 10.0.4.129 | 10.0.4.139 - 10.0.4.255 |
| Lantai 5 (VLAN 105) | 10.0.6.2 - 10.0.6.10 | 10.0.6.1 | 10.0.6.11 - 10.0.6.63 |

#### Kantor Cabang
| Lokasi | VLAN | IP Statis | Gateway | DHCP Pool |
|--------|------|-----------|---------|-----------|
| Regional Office | VLAN 201 | 10.0.6.65 - 10.0.6.70 | 10.0.6.65 | 10.0.6.71 - 10.0.6.126 |

```cisco
! DHCP Pool Example (Layanan Operasional)
ip dhcp pool LAYANAN_OPERASIONAL
 network 10.0.0.0 255.255.254.0
 default-router 10.0.0.1
 dns-server 8.8.8.8 8.8.4.4
 lease 7

ip dhcp excluded-address 10.0.0.1 10.0.0.10

! DHCP Pool (ARA Tech - Lantai 1)
ip dhcp pool ARATECH_L1
 network 10.0.4.0 255.255.255.128
 default-router 10.0.4.1
 dns-server 8.8.8.8 8.8.4.4
 lease 7

ip dhcp excluded-address 10.0.4.1 10.0.4.10
```

---

## 📈 Redundansi dan Skalabilitas

### Link Redundancy
- **OSPF Failover**: Automatic rerouting jika link utama gagal
- **Distribution Layer**: Dual uplinks untuk high availability
- **GRE Tunnel Backup**: Jalur alternatif Utama-Cabang

### Kapasitas Ekspansi

| Lokasi | Alamat Terpakai | Alamat Tersedia | Ruang Ekspansi |
|--------|----------------|-----------------|----------------|
| Gedung Utama | 10.0.0.0 - 10.0.3.239 | 10.0.0.0/21 (2048 host) | ~1100+ alamat |
| Gedung ARA Tech | 10.0.4.0 - 10.0.6.63 | 10.0.4.0/22 (1024 host) | ~448 alamat (10.0.6.64-10.0.7.255) |
| Kantor Cabang | 10.0.6.64 - 10.0.6.126 | 10.0.6.64/26 (62 host) | 12 alamat |

**Catatan**: Semua subnet memiliki cadangan ±20% untuk pertumbuhan immediate.

---

## 🎓 Summary Teknis

### Addressing Scheme
- **Total Network**: 10.0.0.0/20 (4096 addresses)
- **Gedung Utama (VLSM)**: 10.0.0.0/21 - 6 subnets
- **Gedung ARA Tech (CIDR)**: 10.0.4.0/22 - 14 subnets
- **Kantor Cabang**: 10.0.6.64/26 - 1 subnet
- **WAN Links**: 172.16.0.0/28 - 3 links

### VLAN Distribution
- **VLAN 11-16**: Gedung Utama (6 VLANs)
- **VLAN 101-105**: Gedung ARA Tech (5 VLANs)
- **VLAN 201**: Kantor Cabang (1 VLAN)

### Routing Protocol
- **Internal**: Static Routing (intra-building)
- **External**: OSPF Areas 0 & 1 (inter-building)
- **WAN**: GRE Tunnel + OSPF


---

## 📝 Lisensi

Proyek ini dibuat untuk keperluan akademik.

---

**⚡ Built with passion for networking excellence**
