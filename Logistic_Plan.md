# BÁO CÁO DỰ ÁN MẠNG MÁY TÍNH
## NWC204 – SU26
### Trung tâm Logistics & Kho bãi thông minh

---

# PHẦN 1 – TỔNG QUAN DỰ ÁN

## 1.1 Executive Summary (Tóm tắt dự án)

Dự án thiết kế và triển khai hệ thống mạng doanh nghiệp cho **Trung tâm Logistics & Kho bãi thông minh** sử dụng phần mềm mô phỏng **Cisco Packet Tracer**. Hệ thống phục vụ **300 nhân viên**, phân bố tại **2 chi nhánh** với **3 phòng ban** mỗi chi nhánh.

Dự án triển khai đầy đủ **7 dịch vụ mạng** cốt lõi:

| STT | Dịch vụ | Chức năng |
|-----|---------|-----------|
| 1 | DHCP | Cấp phát địa chỉ IP tự động cho các thiết bị |
| 2 | DNS | Phân giải tên miền nội bộ |
| 3 | HTTP/HTTPS | Web Server nội bộ quản lý thông tin logistics |
| 4 | FTP | Server lưu trữ và truyền tải dữ liệu kho bãi |
| 5 | Email | Hệ thống gửi/nhận thư nội bộ |
| 6 | SSH | Quản trị thiết bị mạng từ xa bảo mật |
| 7 | NTP | Đồng bộ thời gian hệ thống toàn mạng |

Dự án thực hiện trong **7 tuần**, chia làm **2 giai đoạn**: thiết kế hạ tầng & định tuyến (Tuần 1–4) và bảo mật & dịch vụ nâng cao (Tuần 5–7).

---

## 1.2 Giới thiệu doanh nghiệp

**Tên doanh nghiệp:** Trung tâm Logistics & Kho bãi thông minh

**Quy mô:**
- Tổng nhân viên: **300 người**
- Số chi nhánh: **2** (Chi nhánh A – Trụ sở chính, Chi nhánh B – Kho vệ tinh)
- Số phòng ban: **3 phòng ban/chi nhánh**

**Danh sách phòng ban:**

| Chi nhánh | Phòng ban | Chức năng |
|-----------|-----------|-----------|
| Chi nhánh A (Trụ sở) | Phòng Điều hành & IT | Quản lý hệ thống, điều phối vận hành |
| Chi nhánh A (Trụ sở) | Phòng Kinh doanh & Khách hàng | Xử lý đơn hàng, quan hệ khách hàng |
| Chi nhánh A (Trụ sở) | Phòng Kho vận | Quản lý nhập/xuất kho, vận chuyển |
| Chi nhánh B (Kho vệ tinh) | Phòng Điều hành & IT | Giám sát hệ thống kho vệ tinh |
| Chi nhánh B (Kho vệ tinh) | Phòng Kho vận | Vận hành kho, kiểm soát hàng hóa |
| Chi nhánh B (Kho vệ tinh) | Phòng Kỹ thuật & Bảo trì | Bảo trì thiết bị, hệ thống IoT kho |

---

## 1.3 Phân tích yêu cầu người dùng và thiết bị

### Yêu cầu người dùng

**Chi nhánh A – Trụ sở chính (180 nhân viên):**

| Phòng ban | Số nhân viên | Yêu cầu chính |
|-----------|--------------|---------------|
| Phòng Điều hành & IT | 30 | SSH quản trị từ xa, DHCP Server, DNS, NTP |
| Phòng Kinh doanh & Khách hàng | 80 | HTTP/HTTPS nội bộ, Email, DHCP |
| Phòng Kho vận | 70 | FTP tải dữ liệu kho, Email, DHCP |

**Chi nhánh B – Kho vệ tinh (120 nhân viên):**

| Phòng ban | Số nhân viên | Yêu cầu chính |
|-----------|--------------|---------------|
| Phòng Điều hành & IT | 20 | SSH, DHCP, NTP |
| Phòng Kho vận | 60 | FTP, Email, DHCP |
| Phòng Kỹ thuật & Bảo trì | 40 | SSH thiết bị, HTTP nội bộ, DHCP |

### Danh sách thiết bị dự kiến

| Thiết bị | Chi nhánh A | Chi nhánh B | Tổng |
|----------|-------------|-------------|------|
| Router Cisco | 1 | 1 | 2 |
| Switch Layer 2 | 3 | 3 | 6 |
| Server (đa dịch vụ) | 2 | 1 | 3 |
| PC/Workstation | 180 | 120 | 300 |
| Printer (mạng) | 3 | 2 | 5 |

---

# GIAI ĐOẠN 1 – THIẾT KẾ HẠ TẦNG, LẬP KẾ HOẠCH IP & ĐỊNH TUYẾN

## (Tuần 1 → Tuần 4)

> **Mục tiêu:** Xây dựng nền tảng kiến trúc mạng và đảm bảo khả năng định tuyến trước khi áp dụng các chính sách bảo mật.

---

## Tuần 1 – Thành lập nhóm & lựa chọn mô hình doanh nghiệp

### Mục tiêu
- Hoàn thành đăng ký nhóm.
- Xác định mô hình doanh nghiệp: **Trung tâm Logistics & Kho bãi thông minh**.

### Công việc thực hiện
- Phân chia vai trò trong nhóm.
- Viết **Phần 1 của báo cáo** (Executive Summary, giới thiệu doanh nghiệp, phân tích yêu cầu – xem mục 1.1–1.3 ở trên).

---

## Tuần 2 – Thiết kế Topology & Chia mạng bằng VLSM

### Mục tiêu
- Hoàn thiện sơ đồ mạng vật lý.
- Tối ưu không gian địa chỉ IPv4 bằng VLSM.

### Ràng buộc kỹ thuật

> ⚠️ **Không được sử dụng:** VLAN, Layer 3 Switch

- Mỗi phòng ban kết nối trực tiếp qua **Router Interface riêng** hoặc **Switch Layer 2 riêng biệt**.
- Kết nối WAN giữa 2 chi nhánh: **Serial** hoặc **Ethernet trực tiếp**.

---

### Thiết kế VLSM

**Địa chỉ mạng gốc:** `192.168.0.0/16`  
**Địa chỉ WAN:** `10.0.0.0/30`

#### Bảng VLSM – Chi nhánh A (Trụ sở chính)

| Subnet | Phòng ban | Số host cần | Subnet Mask | Network Address | Gateway | Usable IP Range | Broadcast |
|--------|-----------|-------------|-------------|-----------------|---------|-----------------|-----------|
| A1 | Phòng Điều hành & IT | 30 | /26 (255.255.255.192) | 192.168.1.0 | 192.168.1.1 | 192.168.1.2 – 192.168.1.62 | 192.168.1.63 |
| A2 | Phòng Kinh doanh | 80 | /25 (255.255.255.128) | 192.168.1.128 | 192.168.1.129 | 192.168.1.130 – 192.168.1.254 | 192.168.1.255 |
| A3 | Phòng Kho vận | 70 | /25 (255.255.255.128) | 192.168.2.0 | 192.168.2.1 | 192.168.2.2 – 192.168.2.126 | 192.168.2.127 |
| A-SRV | Server Farm (CN A) | 10 | /28 (255.255.255.240) | 192.168.1.64 | 192.168.1.65 | 192.168.1.66 – 192.168.1.78 | 192.168.1.79 |

#### Bảng VLSM – Chi nhánh B (Kho vệ tinh)

| Subnet | Phòng ban | Số host cần | Subnet Mask | Network Address | Gateway | Usable IP Range | Broadcast |
|--------|-----------|-------------|-----------------|---------|-----------------|-----------|-----------|
| B1 | Phòng Điều hành & IT | 20 | /27 (255.255.255.224) | 192.168.3.0 | 192.168.3.1 | 192.168.3.2 – 192.168.3.30 | 192.168.3.31 |
| B2 | Phòng Kho vận | 60 | /26 (255.255.255.192) | 192.168.3.64 | 192.168.3.65 | 192.168.3.66 – 192.168.3.126 | 192.168.3.127 |
| B3 | Phòng Kỹ thuật | 40 | /26 (255.255.255.192) | 192.168.3.128 | 192.168.3.129 | 192.168.3.130 – 192.168.3.190 | 192.168.3.191 |
| B-SRV | Server Farm (CN B) | 5 | /29 (255.255.255.248) | 192.168.3.192 | 192.168.3.193 | 192.168.3.194 – 192.168.3.198 | 192.168.3.199 |

#### Bảng WAN – Kết nối liên chi nhánh

| Đường liên kết | Subnet Mask | Network Address | Router A (CN A) | Router B (CN B) | Broadcast |
|----------------|-------------|-----------------|-----------------|-----------------|-----------|
| WAN A–B | /30 (255.255.255.252) | 10.0.0.0 | 10.0.0.1 | 10.0.0.2 | 10.0.0.3 |

---

### Sơ đồ Topology Logic

```
[Chi nhánh A – Trụ sở]                        [Chi nhánh B – Kho vệ tinh]
┌─────────────────────────────┐                ┌─────────────────────────────┐
│  Router-A (192.168.x.x)     │                │  Router-B (192.168.x.x)     │
│  ┌──────────────────────┐   │                │  ┌──────────────────────┐   │
│  │  SW-A1 (IT/Điều hành)│   │  Serial/WAN    │  │  SW-B1 (IT/Điều hành)│   │
│  │  192.168.1.0/26      │   │◄──────────────►│  │  192.168.3.0/27      │   │
│  └──────────────────────┘   │  10.0.0.0/30   │  └──────────────────────┘   │
│  ┌──────────────────────┐   │                │  ┌──────────────────────┐   │
│  │  SW-A2 (Kinh doanh)  │   │                │  │  SW-B2 (Kho vận)     │   │
│  │  192.168.1.128/25    │   │                │  │  192.168.3.64/26     │   │
│  └──────────────────────┘   │                │  └──────────────────────┘   │
│  ┌──────────────────────┐   │                │  ┌──────────────────────┐   │
│  │  SW-A3 (Kho vận)     │   │                │  │  SW-B3 (Kỹ thuật)    │   │
│  │  192.168.2.0/25      │   │                │  │  192.168.3.128/26    │   │
│  └──────────────────────┘   │                │  └──────────────────────┘   │
│  ┌──────────────────────┐   │                │  ┌──────────────────────┐   │
│  │  Server Farm A        │   │                │  │  Server Farm B        │   │
│  │  192.168.1.64/28      │   │                │  │  192.168.3.192/29    │   │
│  └──────────────────────┘   │                │  └──────────────────────┘   │
└─────────────────────────────┘                └─────────────────────────────┘
```

---

## Tuần 3 – Cấu hình nền tảng & Định tuyến liên chi nhánh

### Mục tiêu
Thiết lập khả năng kết nối toàn bộ hệ thống, đảm bảo tất cả thiết bị ping được nhau.

### Cấu hình bảo mật cơ bản

#### Router-A (Chi nhánh A)

```bash
Router> enable
Router# configure terminal

! Đặt tên thiết bị
Router(config)# hostname Router-A

! Banner cảnh báo
Router-A(config)# banner motd #
=========================================================
  TRUNG TAM LOGISTICS & KHO BAI THONG MINH - CHI NHANH A
  CANH BAO: Chi co nhan vien duoc uy quyen moi duoc truy cap!
  Moi truy cap trai phep se bi ghi lai va xu ly.
=========================================================
#

! Mật khẩu enable bảo mật
Router-A(config)# enable secret Logistics@2024

! Bảo mật console
Router-A(config)# line console 0
Router-A(config-line)# password Console@123
Router-A(config-line)# login
Router-A(config-line)# exit

! Bảo mật VTY (SSH)
Router-A(config)# line vty 0 4
Router-A(config-line)# transport input ssh
Router-A(config-line)# login local
Router-A(config-line)# exit

! Mã hóa tất cả mật khẩu
Router-A(config)# service password-encryption
```

#### Cấu hình Interface Router-A

```bash
! Interface kết nối Phòng Điều hành & IT (SW-A1)
Router-A(config)# interface GigabitEthernet0/0
Router-A(config-if)# description PB-DieuHanh-IT-CNA
Router-A(config-if)# ip address 192.168.1.1 255.255.255.192
Router-A(config-if)# no shutdown

! Interface kết nối Phòng Kinh doanh (SW-A2)
Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# description PB-KinhDoanh-CNA
Router-A(config-if)# ip address 192.168.1.129 255.255.255.128
Router-A(config-if)# no shutdown

! Interface kết nối Phòng Kho vận (SW-A3)
Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# description PB-KhoVan-CNA
Router-A(config-if)# ip address 192.168.2.1 255.255.255.128
Router-A(config-if)# no shutdown

! Interface kết nối Server Farm A
Router-A(config)# interface GigabitEthernet0/3
Router-A(config-if)# description ServerFarm-CNA
Router-A(config-if)# ip address 192.168.1.65 255.255.255.240
Router-A(config-if)# no shutdown

! Interface WAN kết nối Chi nhánh B
Router-A(config)# interface Serial0/0/0
Router-A(config-if)# description WAN-Link-CNA-to-CNB
Router-A(config-if)# ip address 10.0.0.1 255.255.255.252
Router-A(config-if)# clock rate 64000
Router-A(config-if)# no shutdown
```

#### Cấu hình Static Routing – Router-A

```bash
! Static route tới tất cả subnet Chi nhánh B
Router-A(config)# ip route 192.168.3.0 255.255.255.224 10.0.0.2
Router-A(config)# ip route 192.168.3.64 255.255.255.192 10.0.0.2
Router-A(config)# ip route 192.168.3.128 255.255.255.192 10.0.0.2
Router-A(config)# ip route 192.168.3.192 255.255.255.248 10.0.0.2

Router-A(config)# end
Router-A# write memory
```

#### Cấu hình Router-B (Chi nhánh B) – tương tự

```bash
Router> enable
Router# configure terminal
Router(config)# hostname Router-B

Router-B(config)# banner motd #
=========================================================
  TRUNG TAM LOGISTICS & KHO BAI THONG MINH - CHI NHANH B
  CANH BAO: Chi co nhan vien duoc uy quyen moi duoc truy cap!
=========================================================
#

Router-B(config)# enable secret Logistics@2024

! Interface kết nối Phòng Điều hành & IT (SW-B1)
Router-B(config)# interface GigabitEthernet0/0
Router-B(config-if)# ip address 192.168.3.1 255.255.255.224
Router-B(config-if)# no shutdown

! Interface kết nối Phòng Kho vận (SW-B2)
Router-B(config)# interface GigabitEthernet0/1
Router-B(config-if)# ip address 192.168.3.65 255.255.255.192
Router-B(config-if)# no shutdown

! Interface kết nối Phòng Kỹ thuật (SW-B3)
Router-B(config)# interface GigabitEthernet0/2
Router-B(config-if)# ip address 192.168.3.129 255.255.255.192
Router-B(config-if)# no shutdown

! Interface kết nối Server Farm B
Router-B(config)# interface GigabitEthernet0/3
Router-B(config-if)# ip address 192.168.3.193 255.255.255.248
Router-B(config-if)# no shutdown

! Interface WAN
Router-B(config)# interface Serial0/0/0
Router-B(config-if)# ip address 10.0.0.2 255.255.255.252
Router-B(config-if)# no shutdown

! Static route tới Chi nhánh A
Router-B(config)# ip route 192.168.1.0 255.255.255.192 10.0.0.1
Router-B(config)# ip route 192.168.1.64 255.255.255.240 10.0.0.1
Router-B(config)# ip route 192.168.1.128 255.255.255.128 10.0.0.1
Router-B(config)# ip route 192.168.2.0 255.255.255.128 10.0.0.1

Router-B(config)# end
Router-B# write memory
```

### Điều kiện bắt buộc

> ✅ Tất cả thiết bị phải **ping được nhau** trước khi chuyển sang Tuần 4.

Kiểm tra bằng lệnh:

```bash
! Từ PC bất kỳ ở CN A ping tới CN B
ping 192.168.3.66

! Từ Router-A ping Router-B
ping 10.0.0.2
ping 192.168.3.1
```

---

## Tuần 4 – Dịch vụ mạng & Đánh giá giữa kỳ

### Mục tiêu
- Tự động cấp phát IP (DHCP).
- Phân giải tên miền nội bộ (DNS).
- Kiểm tra HTTP Server nội bộ.

---

### Cấu hình DHCP

#### Phương án: Dùng Router làm DHCP Server

```bash
! === DHCP Server trên Router-A ===

! DHCP Pool cho Phòng Điều hành & IT (CN A)
Router-A(config)# ip dhcp pool POOL-A1-IT
Router-A(dhcp-config)# network 192.168.1.0 255.255.255.192
Router-A(dhcp-config)# default-router 192.168.1.1
Router-A(dhcp-config)# dns-server 192.168.1.66
Router-A(dhcp-config)# domain-name logistics.local
Router-A(dhcp-config)# lease 1

! DHCP Pool cho Phòng Kinh doanh (CN A)
Router-A(config)# ip dhcp pool POOL-A2-KD
Router-A(dhcp-config)# network 192.168.1.128 255.255.255.128
Router-A(dhcp-config)# default-router 192.168.1.129
Router-A(dhcp-config)# dns-server 192.168.1.66
Router-A(dhcp-config)# domain-name logistics.local
Router-A(dhcp-config)# lease 1

! DHCP Pool cho Phòng Kho vận (CN A)
Router-A(config)# ip dhcp pool POOL-A3-KV
Router-A(dhcp-config)# network 192.168.2.0 255.255.255.128
Router-A(dhcp-config)# default-router 192.168.2.1
Router-A(dhcp-config)# dns-server 192.168.1.66
Router-A(dhcp-config)# domain-name logistics.local
Router-A(dhcp-config)# lease 1

! Loại trừ IP Gateway và Server khỏi pool DHCP
Router-A(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
Router-A(config)# ip dhcp excluded-address 192.168.1.65 192.168.1.75
Router-A(config)# ip dhcp excluded-address 192.168.1.129 192.168.1.135
Router-A(config)# ip dhcp excluded-address 192.168.2.1 192.168.2.5
```

#### IP Helper-Address (DHCP Relay)

> Vì mỗi subnet đi qua Router Interface riêng, cần cấu hình `ip helper-address` để DHCP broadcast được chuyển tiếp:

```bash
! Nếu dùng Server riêng làm DHCP (IP: 192.168.1.66)
! Thêm helper-address trên mỗi interface của Router

Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# ip helper-address 192.168.1.66

Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# ip helper-address 192.168.1.66
```

---

### Cấu hình DNS

#### Cài đặt DNS Server (tại Server Farm A – 192.168.1.66)

**Bật dịch vụ DNS trên Server:**

| Tên miền nội bộ | Loại Record | Địa chỉ IP |
|-----------------|-------------|------------|
| `logistics.local` | A Record | 192.168.1.67 (Web Server) |
| `www.logistics.local` | A Record | 192.168.1.67 |
| `ftp.logistics.local` | A Record | 192.168.1.68 (FTP Server) |
| `mail.logistics.local` | A Record | 192.168.1.69 (Email Server) |
| `ntp.logistics.local` | A Record | 192.168.1.66 (NTP Server) |

> **Lưu ý Cisco Packet Tracer:** Vào tab **Services → DNS** trên Server, bật **ON**, sau đó thêm từng record.

---

### Địa chỉ IP tĩnh cho Server Farm

#### Server Farm A (Chi nhánh A)

| Server | IP Address | Subnet Mask | Gateway | Dịch vụ |
|--------|-----------|-------------|---------|---------|
| Server-A1 | 192.168.1.66 | 255.255.255.240 | 192.168.1.65 | DHCP, DNS, NTP |
| Server-A2 | 192.168.1.67 | 255.255.255.240 | 192.168.1.65 | HTTP/HTTPS, Email |
| Server-A3 | 192.168.1.68 | 255.255.255.240 | 192.168.1.65 | FTP |

#### Server Farm B (Chi nhánh B)

| Server | IP Address | Subnet Mask | Gateway | Dịch vụ |
|--------|-----------|-------------|---------|---------|
| Server-B1 | 192.168.3.194 | 255.255.255.248 | 192.168.3.193 | DHCP (CN B), DNS relay |

---

# MIDTERM CHECKPOINT (Cuối tuần 4)

## Hình thức

> 🖥️ **Demo trực tiếp** file Packet Tracer với giảng viên.

## Checklist kiểm tra

### 1. Kiểm tra Topology

- [ ] Không sử dụng VLAN
- [ ] Không sử dụng Layer 3 Switch
- [ ] Mỗi phòng ban có Switch Layer 2 riêng
- [ ] 2 chi nhánh kết nối WAN qua Serial/Ethernet

### 2. Kiểm tra VLSM

- [ ] Bảng VLSM đầy đủ Network Address, Gateway, Usable Range, Subnet Mask
- [ ] Tính toán VLSM chính xác, không chồng chéo subnet
- [ ] Tài liệu khớp với cấu hình thực tế trên Packet Tracer

### 3. Kiểm tra thực tế (Giảng viên chọn ngẫu nhiên)

- [ ] ✅ PC nhận IP tự động qua DHCP
- [ ] ✅ PC ping được Server nội bộ
- [ ] ✅ PC ping được thiết bị ở chi nhánh khác
- [ ] ✅ Truy cập `logistics.local` qua trình duyệt thành công

> ⚠️ **Phải đạt Midterm Checkpoint mới được vào Giai đoạn 2.**

---

# GIAI ĐOẠN 2 – BẢO MẬT & DỊCH VỤ NÂNG CAO

## (Tuần 5 → Tuần 7)

> **Mục tiêu:** Triển khai đầy đủ 7 dịch vụ mạng, áp dụng bảo mật SSH, cấu hình NTP và FTP.

---

## Tuần 5 – Cấu hình SSH & Bảo mật nâng cao

### Cấu hình SSH trên Router

```bash
! Tạo domain name (bắt buộc trước khi generate key)
Router-A(config)# ip domain-name logistics.local

! Tạo local user cho SSH
Router-A(config)# username admin privilege 15 secret SSH@Logistics2024
Router-A(config)# username netops privilege 10 secret NetOps@123

! Generate RSA key (dùng 2048 bit)
Router-A(config)# crypto key generate rsa modulus 2048

! Chỉ cho phép SSH version 2
Router-A(config)# ip ssh version 2
Router-A(config)# ip ssh time-out 60
Router-A(config)# ip ssh authentication-retries 3

! Cấu hình VTY chỉ nhận SSH
Router-A(config)# line vty 0 4
Router-A(config-line)# transport input ssh
Router-A(config-line)# login local
Router-A(config-line)# exec-timeout 5 0
```

---

## Tuần 6 – Cấu hình FTP, Email & NTP

### Cấu hình NTP

```bash
! Router-A làm NTP Master (stratum 1 cho nội bộ)
Router-A(config)# ntp master 3
Router-A(config)# ntp update-calendar

! Router-B đồng bộ từ Router-A
Router-B(config)# ntp server 10.0.0.1
```

**Cấu hình NTP Server trong Packet Tracer:**
- Vào Server-A1 → **Services → NTP**
- Bật **ON**, đặt ngày giờ chính xác

### Cấu hình FTP Server (Packet Tracer)

Vào Server-A3 → **Services → FTP:**
- Bật **ON**
- Tạo User: `ftp_logistics` / Password: `FTP@Logistics`
- Quyền: Read, Write, Delete, Rename, List

### Cấu hình Email Server (Packet Tracer)

Vào Server-A2 → **Services → EMAIL:**
- Bật **SMTP ON** và **POP3 ON**
- Domain: `logistics.local`
- Tạo tài khoản người dùng mẫu: `admin@logistics.local`

---

## Tuần 7 – Kiểm thử toàn hệ thống & Hoàn thiện báo cáo

### Checklist kiểm thử cuối kỳ

| STT | Dịch vụ | Bài kiểm thử | Kết quả mong đợi |
|-----|---------|-------------|-----------------|
| 1 | DHCP | PC tự động nhận IP | IP đúng subnet, đúng gateway, đúng DNS |
| 2 | DNS | Ping `logistics.local` | Phân giải thành 192.168.1.67 |
| 3 | HTTP | Truy cập `http://logistics.local` | Trang web nội bộ hiển thị |
| 4 | FTP | Kết nối FTP tới ftp.logistics.local | Upload/Download file thành công |
| 5 | Email | Gửi mail nội bộ | Nhận được mail trong hòm thư |
| 6 | SSH | SSH từ PC tới Router | Đăng nhập thành công, bảo mật |
| 7 | NTP | Show clock trên Router | Thời gian đồng bộ chính xác |
| 8 | Ping liên chi nhánh | CN A ↔ CN B | Ping thành công < 10ms |

---

# PHỤ LỤC – BẢNG TỔNG HỢP ĐỊA CHỈ IP

## Tổng hợp toàn bộ hệ thống

| Thiết bị | Interface | IP Address | Subnet Mask | Ghi chú |
|----------|-----------|-----------|-------------|---------|
| Router-A | Gi0/0 | 192.168.1.1 | /26 | Gateway PB IT (CN A) |
| Router-A | Gi0/1 | 192.168.1.129 | /25 | Gateway PB Kinh doanh (CN A) |
| Router-A | Gi0/2 | 192.168.2.1 | /25 | Gateway PB Kho vận (CN A) |
| Router-A | Gi0/3 | 192.168.1.65 | /28 | Gateway Server Farm A |
| Router-A | Se0/0/0 | 10.0.0.1 | /30 | WAN link tới CN B |
| Router-B | Gi0/0 | 192.168.3.1 | /27 | Gateway PB IT (CN B) |
| Router-B | Gi0/1 | 192.168.3.65 | /26 | Gateway PB Kho vận (CN B) |
| Router-B | Gi0/2 | 192.168.3.129 | /26 | Gateway PB Kỹ thuật (CN B) |
| Router-B | Gi0/3 | 192.168.3.193 | /29 | Gateway Server Farm B |
| Router-B | Se0/0/0 | 10.0.0.2 | /30 | WAN link tới CN A |
| Server-A1 | NIC | 192.168.1.66 | /28 | DHCP + DNS + NTP |
| Server-A2 | NIC | 192.168.1.67 | /28 | HTTP/HTTPS + Email |
| Server-A3 | NIC | 192.168.1.68 | /28 | FTP |
| Server-B1 | NIC | 192.168.3.194 | /29 | DHCP (CN B) |

---

## Timeline tổng quan

```
Tuần 1    Tuần 2    Tuần 3    Tuần 4    [MIDTERM]  Tuần 5    Tuần 6    Tuần 7
  │         │         │         │            │         │         │         │
  ▼         ▼         ▼         ▼            ▼         ▼         ▼         ▼
Thành    Topology  Cấu hình  DHCP &     CHECKPOINT  SSH &    FTP, NTP,  Kiểm thử
lập      & VLSM    định      DNS                   Bảo mật   Email     & Báo cáo
nhóm                tuyến
         ◄──────────── GIAI ĐOẠN 1 ────────────►  ◄────── GIAI ĐOẠN 2 ──────►
```

---

*Tài liệu được soạn thảo cho môn NWC204 – SU26*  
*Mô hình: Trung tâm Logistics & Kho bãi thông minh*  
*Phần mềm mô phỏng: Cisco Packet Tracer*