# TÀI LIỆU HỆ THỐNG MẠNG – TRUNG TÂM LOGISTICS & KHO BÃI THÔNG MINH
### Môn: NWC204 – SU26 | Phần mềm: Cisco Packet Tracer
### Phiên bản: Cập nhật theo Milestone – Cô lập phòng ban (ACL) · Server tập trung · Bổ sung Chi nhánh C

---

## 📌 TÓM TẮT CÁC THAY ĐỔI SO VỚI BẢN TRƯỚC (theo README – Milestone Update)

| # | Yêu cầu của giảng viên | Thay đổi áp dụng trong tài liệu này |
|---|---|---|
| 1 | Máy tính chỉ ping được trong cùng phòng ban; vẫn truy cập được Server | Cấu hình **ACL** trên Router-A, Router-B, Router-C để chặn ICMP giữa các phòng ban trong cùng chi nhánh, đồng thời cho phép toàn bộ lưu lượng tới Server |
| 2 | Chỉ dùng đúng 1 Server | Gộp Server-A1 + Server-A2 + Server-A3 + Server-B1 thành **Server-CORE** duy nhất (192.168.1.66/28), loại bỏ SW-BSRV |
| 3 | Router kết nối Server qua Switch | Giữ nguyên mô hình `Router → Switch → Server` (SW-SERVER) |
| 4 | Thêm 1 chi nhánh, mô hình có 1 nhánh chính + 2 nhánh phụ | Bổ sung **Chi nhánh C – Trạm trung chuyển**; Chi nhánh A là **nhánh chính (hub)**, Chi nhánh B và C là **2 nhánh phụ**, kết nối dạng hub-and-spoke |

---

# PHẦN 1 – BẢNG KẾT NỐI THIẾT BỊ

## 1.1 Bảng Kết nối: Thiết bị → Thiết bị

| STT | Thiết bị Nguồn | Thiết bị Đích | Dây Kết nối | Cổng Nguồn | Cổng Đích |
|-----|---------------|--------------|-------------|------------|-----------|
| 1 | Router-A | SW-A1 (IT/Điều hành CN A) | Copper Straight-Through | GigabitEthernet0/0 | FastEthernet0/1 |
| 2 | Router-A | SW-A2 (Kinh doanh CN A) | Copper Straight-Through | GigabitEthernet0/1 | FastEthernet0/1 |
| 3 | Router-A | SW-A3 (Kho vận CN A) | Copper Straight-Through | GigabitEthernet0/2 | FastEthernet0/1 |
| 4 | Router-A | SW-SERVER (Server-CORE) | Copper Straight-Through | GigabitEthernet0/3 | FastEthernet0/1 |
| 5 | Router-A | Router-B (WAN Chính → Phụ 1) | Serial DCE/DTE | Serial0/0/0 | Serial0/0/0 |
| 6 | Router-A | Router-C (WAN Chính → Phụ 2) | Serial DCE/DTE | Serial0/0/1 | Serial0/0/0 |
| 7 | Router-B | SW-B1 (IT/Điều hành CN B) | Copper Straight-Through | GigabitEthernet0/0 | FastEthernet0/1 |
| 8 | Router-B | SW-B2 (Kho vận CN B) | Copper Straight-Through | GigabitEthernet0/1 | FastEthernet0/1 |
| 9 | Router-B | SW-B3 (Kỹ thuật CN B) | Copper Straight-Through | GigabitEthernet0/2 | FastEthernet0/1 |
| 10 | Router-C | SW-C1 (IT/Điều hành CN C) | Copper Straight-Through | GigabitEthernet0/0 | FastEthernet0/1 |
| 11 | Router-C | SW-C2 (Vận chuyển CN C) | Copper Straight-Through | GigabitEthernet0/1 | FastEthernet0/1 |
| 12 | Router-C | SW-C3 (Đóng gói CN C) | Copper Straight-Through | GigabitEthernet0/2 | FastEthernet0/1 |
| 13 | SW-A1 | PC-A1-01 → PC-A1-30 | Copper Straight-Through | FastEthernet0/2 → Fa0/31 | FastEthernet0 |
| 14 | SW-A2 | PC-A2-01 → PC-A2-80 | Copper Straight-Through | FastEthernet0/2 → Fa0/81 | FastEthernet0 |
| 15 | SW-A3 | PC-A3-01 → PC-A3-70 | Copper Straight-Through | FastEthernet0/2 → Fa0/71 | FastEthernet0 |
| 16 | SW-SERVER | Server-CORE | Copper Straight-Through | FastEthernet0/2 | FastEthernet0 |
| 17 | SW-B1 | PC-B1-01 → PC-B1-20 | Copper Straight-Through | FastEthernet0/2 → Fa0/21 | FastEthernet0 |
| 18 | SW-B2 | PC-B2-01 → PC-B2-60 | Copper Straight-Through | FastEthernet0/2 → Fa0/61 | FastEthernet0 |
| 19 | SW-B3 | PC-B3-01 → PC-B3-40 | Copper Straight-Through | FastEthernet0/2 → Fa0/41 | FastEthernet0 |
| 20 | SW-C1 | PC-C1-01 → PC-C1-20 | Copper Straight-Through | FastEthernet0/2 → Fa0/21 | FastEthernet0 |
| 21 | SW-C2 | PC-C2-01 → PC-C2-50 | Copper Straight-Through | FastEthernet0/2 → Fa0/51 | FastEthernet0 |
| 22 | SW-C3 | PC-C3-01 → PC-C3-30 | Copper Straight-Through | FastEthernet0/2 → Fa0/31 | FastEthernet0 |
| 23 | SW-A1 | Printer-A1 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 24 | SW-A2 | Printer-A2 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 25 | SW-A3 | Printer-A3 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 26 | SW-B1 | Printer-B1 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 27 | SW-B2 | Printer-B2 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 28 | SW-C1 | Printer-C1 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 29 | SW-C2 | Printer-C2 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |

> **Ghi chú:**
> - Router-A là **nhánh chính (hub)**, đóng vai trò trung chuyển cho cả 2 nhánh phụ. Router-A ↔ Router-B và Router-A ↔ Router-C đều dùng cáp Serial (DCE đặt tại Router-A, clock rate 64000).
> - Router-B ↔ Router-C **không** có liên kết trực tiếp; mọi lưu lượng giữa CN B và CN C đều đi qua Router-A.
> - Server-A2, Server-A3, Server-B1 và SW-BSRV đã được **loại bỏ** khỏi mô hình (gộp vào Server-CORE).

---

# PHẦN 2 – SƠ ĐỒ MẠNG (NETWORK TOPOLOGY)

## 2.1 Mô hình tổng quan (Hub-and-Spoke)

```
NHÁNH CHÍNH – CHI NHÁNH A (Trụ sở chính)
└── ROUTER-A  (hub trung tâm – định tuyến cho cả CN B và CN C)
    ├── Gi0/0  192.168.1.1/26    ── SW-A1 ── IT & Điều hành     (30 PC + Printer)   192.168.1.0/26
    ├── Gi0/1  192.168.1.129/25  ── SW-A2 ── Kinh doanh         (80 PC + Printer)   192.168.1.128/25
    ├── Gi0/2  192.168.2.1/25    ── SW-A3 ── Kho vận            (70 PC + Printer)   192.168.2.0/25
    ├── Gi0/3  192.168.1.65/28   ── SW-SERVER ── Server-CORE (192.168.1.66)         192.168.1.64/28
    │                                            DHCP · DNS · HTTP/HTTPS · FTP · Email · NTP
    ├── Se0/0/0  10.0.0.1 (DCE)  ── WAN A↔B  10.0.0.0/30  ─────────────┐
    └── Se0/0/1  10.0.0.5 (DCE)  ── WAN A↔C  10.0.0.4/30  ──────┐      │
                                                                 │      │
        NHÁNH PHỤ 1 – CHI NHÁNH B (Kho vệ tinh)   ◄──────────────┼──────┘
        └── ROUTER-B   Se0/0/0: 10.0.0.2 (DTE)                  │
            ├── Gi0/0  192.168.3.1/27   ── SW-B1 ── IT & Điều hành (20 PC + Printer)  192.168.3.0/27
            ├── Gi0/1  192.168.3.65/26  ── SW-B2 ── Kho vận        (60 PC + Printer)  192.168.3.64/26
            └── Gi0/2  192.168.3.129/26 ── SW-B3 ── Kỹ thuật       (40 PC)            192.168.3.128/26

        NHÁNH PHỤ 2 – CHI NHÁNH C (Trạm trung chuyển)  ◄────────────────────┘
        └── ROUTER-C   Se0/0/0: 10.0.0.6 (DTE)
            ├── Gi0/0  192.168.4.1/27   ── SW-C1 ── IT & Điều hành (20 PC + Printer)  192.168.4.0/27
            ├── Gi0/1  192.168.4.65/26  ── SW-C2 ── Vận chuyển     (50 PC + Printer)  192.168.4.64/26
            └── Gi0/2  192.168.4.129/26 ── SW-C3 ── Đóng gói       (30 PC)            192.168.4.128/26
```

> **Ý nghĩa hub-and-spoke:** Router-A vừa là router trụ sở chính, vừa là điểm trung chuyển định tuyến (transit) duy nhất giữa Chi nhánh B và Chi nhánh C. Toàn bộ lưu lượng B↔C đi qua Router-A. Đây cũng là router duy nhất kết nối tới Server-CORE, nên Server-CORE luôn cách Router-A đúng 1 hop, đảm bảo tốc độ truy cập đồng đều cho cả 3 chi nhánh.

## 2.2 Bảng Tóm tắt Địa chỉ IP Toàn hệ thống

| Thiết bị | Interface | IP Address | Subnet Mask | Ghi chú |
|----------|-----------|-----------|-------------|---------|
| Router-A | Gi0/0 | 192.168.1.1 | 255.255.255.192 (/26) | Gateway PB IT – CN A |
| Router-A | Gi0/1 | 192.168.1.129 | 255.255.255.128 (/25) | Gateway PB Kinh doanh – CN A |
| Router-A | Gi0/2 | 192.168.2.1 | 255.255.255.128 (/25) | Gateway PB Kho vận – CN A |
| Router-A | Gi0/3 | 192.168.1.65 | 255.255.255.240 (/28) | Gateway Server-CORE |
| Router-A | Se0/0/0 | 10.0.0.1 | 255.255.255.252 (/30) | WAN tới CN B – nhánh phụ 1 (DCE) |
| Router-A | Se0/0/1 | 10.0.0.5 | 255.255.255.252 (/30) | WAN tới CN C – nhánh phụ 2 (DCE) |
| Router-B | Gi0/0 | 192.168.3.1 | 255.255.255.224 (/27) | Gateway PB IT – CN B |
| Router-B | Gi0/1 | 192.168.3.65 | 255.255.255.192 (/26) | Gateway PB Kho vận – CN B |
| Router-B | Gi0/2 | 192.168.3.129 | 255.255.255.192 (/26) | Gateway PB Kỹ thuật – CN B |
| Router-B | Se0/0/0 | 10.0.0.2 | 255.255.255.252 (/30) | WAN tới CN A (DTE) |
| Router-C | Gi0/0 | 192.168.4.1 | 255.255.255.224 (/27) | Gateway PB IT – CN C |
| Router-C | Gi0/1 | 192.168.4.65 | 255.255.255.192 (/26) | Gateway PB Vận chuyển – CN C |
| Router-C | Gi0/2 | 192.168.4.129 | 255.255.255.192 (/26) | Gateway PB Đóng gói – CN C |
| Router-C | Se0/0/0 | 10.0.0.6 | 255.255.255.252 (/30) | WAN tới CN A (DTE) |
| Server-CORE | NIC | 192.168.1.66 | 255.255.255.240 (/28) | DHCP+DNS+HTTP+HTTPS+FTP+Email+NTP (toàn hệ thống) |

---

# PHẦN 3 – DANH SÁCH LỆNH CẤU HÌNH

## 3.1 Cấu hình Router-A (Chi nhánh A – Nhánh chính / Hub)

### 3.1.1 Cấu hình cơ bản & bảo mật

```bash
Router> enable
Router# configure terminal

Router(config)# hostname Router-A

Router-A(config)# banner motd #
=========================================================
  TRUNG TAM LOGISTICS & KHO BAI THONG MINH - CHI NHANH A
  CANH BAO: Chi co nhan vien duoc uy quyen moi duoc truy cap!
  Moi truy cap trai phep se bi ghi lai va xu ly.
=========================================================
#

Router-A(config)# enable secret Logistics@2024

Router-A(config)# line console 0
Router-A(config-line)# password Console@123
Router-A(config-line)# login
Router-A(config-line)# exit

Router-A(config)# line vty 0 4
Router-A(config-line)# transport input ssh
Router-A(config-line)# login local
Router-A(config-line)# exec-timeout 5 0
Router-A(config-line)# exit

Router-A(config)# service password-encryption
```

### 3.1.2 Cấu hình Interface Router-A (bổ sung Se0/0/1 cho CN C)

```bash
! Interface kết nối Phòng IT/Điều hành (SW-A1)
Router-A(config)# interface GigabitEthernet0/0
Router-A(config-if)# description PB-DieuHanh-IT-CNA
Router-A(config-if)# ip address 192.168.1.1 255.255.255.192
Router-A(config-if)# no shutdown
Router-A(config-if)# exit

! Interface kết nối Phòng Kinh doanh (SW-A2)
Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# description PB-KinhDoanh-CNA
Router-A(config-if)# ip address 192.168.1.129 255.255.255.128
Router-A(config-if)# no shutdown
Router-A(config-if)# exit

! Interface kết nối Phòng Kho vận (SW-A3)
Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# description PB-KhoVan-CNA
Router-A(config-if)# ip address 192.168.2.1 255.255.255.128
Router-A(config-if)# no shutdown
Router-A(config-if)# exit

! Interface kết nối SW-SERVER (Server-CORE)
Router-A(config)# interface GigabitEthernet0/3
Router-A(config-if)# description ServerCore-CNA
Router-A(config-if)# ip address 192.168.1.65 255.255.255.240
Router-A(config-if)# no shutdown
Router-A(config-if)# exit

! Interface WAN kết nối Chi nhánh B – nhánh phụ 1 (DCE – cấp clock)
Router-A(config)# interface Serial0/0/0
Router-A(config-if)# description WAN-Link-CNA-to-CNB
Router-A(config-if)# ip address 10.0.0.1 255.255.255.252
Router-A(config-if)# clock rate 64000
Router-A(config-if)# no shutdown
Router-A(config-if)# exit

! Interface WAN kết nối Chi nhánh C – nhánh phụ 2 (DCE – cấp clock) [MỚI]
Router-A(config)# interface Serial0/0/1
Router-A(config-if)# description WAN-Link-CNA-to-CNC
Router-A(config-if)# ip address 10.0.0.5 255.255.255.252
Router-A(config-if)# clock rate 64000
Router-A(config-if)# no shutdown
Router-A(config-if)# exit
```

### 3.1.3 Cấu hình Static Routing – Router-A (hub, định tuyến cho cả 2 nhánh phụ)

```bash
! Định tuyến tới các subnet Chi nhánh B
Router-A(config)# ip route 192.168.3.0 255.255.255.224 10.0.0.2
Router-A(config)# ip route 192.168.3.64 255.255.255.192 10.0.0.2
Router-A(config)# ip route 192.168.3.128 255.255.255.192 10.0.0.2

! Định tuyến tới các subnet Chi nhánh C [MỚI]
Router-A(config)# ip route 192.168.4.0 255.255.255.224 10.0.0.6
Router-A(config)# ip route 192.168.4.64 255.255.255.192 10.0.0.6
Router-A(config)# ip route 192.168.4.128 255.255.255.192 10.0.0.6

Router-A(config)# end
Router-A# write memory
```

### 3.1.4 ACL cô lập phòng ban – Chặn ping liên-phòng-ban, cho phép truy cập Server [MỚI – theo yêu cầu #1]

```bash
Router-A(config)# access-list 100 remark Chan ping giua cac phong ban CN-A, van cho phep truy cap Server-CORE

! IT (192.168.1.0/26) <-> Kinh doanh (192.168.1.128/25)
Router-A(config)# access-list 100 deny icmp 192.168.1.0 0.0.0.63 192.168.1.128 0.0.0.127
Router-A(config)# access-list 100 deny icmp 192.168.1.128 0.0.0.127 192.168.1.0 0.0.0.63

! IT (192.168.1.0/26) <-> Kho vận (192.168.2.0/25)
Router-A(config)# access-list 100 deny icmp 192.168.1.0 0.0.0.63 192.168.2.0 0.0.0.127
Router-A(config)# access-list 100 deny icmp 192.168.2.0 0.0.0.127 192.168.1.0 0.0.0.63

! Kinh doanh (192.168.1.128/25) <-> Kho vận (192.168.2.0/25)
Router-A(config)# access-list 100 deny icmp 192.168.1.128 0.0.0.127 192.168.2.0 0.0.0.127
Router-A(config)# access-list 100 deny icmp 192.168.2.0 0.0.0.127 192.168.1.128 0.0.0.127

! Cho phép tất cả lưu lượng còn lại (bao gồm truy cập Server-CORE, DHCP, HTTP, FTP...)
Router-A(config)# access-list 100 permit ip any any
```

Áp dụng ACL trên các interface phòng ban (không áp dụng trên Gi0/3 vì đó là đường tới Server-CORE):

```bash
Router-A(config)# interface GigabitEthernet0/0
Router-A(config-if)# ip access-group 100 in
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# ip access-group 100 in
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# ip access-group 100 in
Router-A(config-if)# exit
```

> **Lưu ý phạm vi ACL:** ACL 100 chỉ cô lập 3 phòng ban *nội bộ* của Chi nhánh A. Lưu lượng từ/đến Chi nhánh B, Chi nhánh C qua Serial vẫn được định tuyến bình thường (không bị ACL này chặn), vì yêu cầu của giảng viên là cô lập phòng ban trong cùng một chi nhánh, không phải cô lập giữa các chi nhánh.

### 3.1.5 SSH – Router-A

```bash
Router-A# configure terminal
Router-A(config)# ip domain-name logistics.local
Router-A(config)# username admin privilege 15 secret SSH@Logistics2024
Router-A(config)# username netops privilege 10 secret NetOps@123
Router-A(config)# crypto key generate rsa modulus 2048
Router-A(config)# ip ssh version 2
Router-A(config)# ip ssh time-out 60
Router-A(config)# ip ssh authentication-retries 3
Router-A(config)# line vty 0 4
Router-A(config-line)# transport input ssh
Router-A(config-line)# login local
Router-A(config-line)# exec-timeout 5 0
Router-A(config-line)# exit
```

### 3.1.6 NTP – Router-A (NTP Master cho toàn hệ thống)

```bash
Router-A(config)# ntp master 3
Router-A(config)# ntp update-calendar
Router-A(config)# clock timezone ICT 7
```

### 3.1.7 DHCP Relay (IP Helper-Address) – Router-A [CẬP NHẬT theo yêu cầu #2]

> Vì Server-CORE đặt trên subnet riêng (192.168.1.64/28, khác với 3 subnet phòng ban), Router-A cần relay broadcast DHCP từ cả 3 phòng ban về Server-CORE.

```bash
Router-A(config)# interface GigabitEthernet0/0
Router-A(config-if)# ip helper-address 192.168.1.66
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# ip helper-address 192.168.1.66
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# ip helper-address 192.168.1.66
Router-A(config-if)# exit

Router-A(config)# end
Router-A# write memory
```

---

## 3.2 Cấu hình Router-B (Chi nhánh B – Nhánh phụ 1 / Kho vệ tinh)

### 3.2.1 Cấu hình cơ bản & bảo mật

```bash
Router> enable
Router# configure terminal
Router(config)# hostname Router-B

Router-B(config)# banner motd #
=========================================================
  TRUNG TAM LOGISTICS & KHO BAI THONG MINH - CHI NHANH B
  CANH BAO: Chi co nhan vien duoc uy quyen moi duoc truy cap!
  Moi truy cap trai phep se bi ghi lai va xu ly.
=========================================================
#

Router-B(config)# enable secret Logistics@2024
Router-B(config)# service password-encryption

Router-B(config)# line console 0
Router-B(config-line)# password Console@123
Router-B(config-line)# login
Router-B(config-line)# exit

Router-B(config)# line vty 0 4
Router-B(config-line)# transport input ssh
Router-B(config-line)# login local
Router-B(config-line)# exec-timeout 5 0
Router-B(config-line)# exit
```

### 3.2.2 Cấu hình Interface Router-B

```bash
! Interface kết nối Phòng IT/Điều hành (SW-B1)
Router-B(config)# interface GigabitEthernet0/0
Router-B(config-if)# description PB-DieuHanh-IT-CNB
Router-B(config-if)# ip address 192.168.3.1 255.255.255.224
Router-B(config-if)# no shutdown
Router-B(config-if)# exit

! Interface kết nối Phòng Kho vận (SW-B2)
Router-B(config)# interface GigabitEthernet0/1
Router-B(config-if)# description PB-KhoVan-CNB
Router-B(config-if)# ip address 192.168.3.65 255.255.255.192
Router-B(config-if)# no shutdown
Router-B(config-if)# exit

! Interface kết nối Phòng Kỹ thuật (SW-B3)
Router-B(config)# interface GigabitEthernet0/2
Router-B(config-if)# description PB-KyThuat-CNB
Router-B(config-if)# ip address 192.168.3.129 255.255.255.192
Router-B(config-if)# no shutdown
Router-B(config-if)# exit

! Lưu ý: Gi0/3 (trước đây nối SW-BSRV/Server-B1) ĐÃ NGẮT – không còn server cục bộ ở CN B
Router-B(config)# interface GigabitEthernet0/3
Router-B(config-if)# shutdown
Router-B(config-if)# description KHONG-SU-DUNG-Du-phong-mo-rong
Router-B(config-if)# exit

! Interface WAN kết nối Chi nhánh A (DTE)
Router-B(config)# interface Serial0/0/0
Router-B(config-if)# description WAN-Link-CNB-to-CNA
Router-B(config-if)# ip address 10.0.0.2 255.255.255.252
Router-B(config-if)# no shutdown
Router-B(config-if)# exit
```

### 3.2.3 Cấu hình Static Routing – Router-B (mọi tuyến đều qua Router-A – hub)

```bash
! Định tuyến tới các subnet Chi nhánh A (bao gồm subnet Server-CORE)
Router-B(config)# ip route 192.168.1.0 255.255.255.192 10.0.0.1
Router-B(config)# ip route 192.168.1.64 255.255.255.240 10.0.0.1
Router-B(config)# ip route 192.168.1.128 255.255.255.128 10.0.0.1
Router-B(config)# ip route 192.168.2.0 255.255.255.128 10.0.0.1

! Định tuyến tới các subnet Chi nhánh C – đi xuyên qua Router-A (hub) [MỚI]
Router-B(config)# ip route 192.168.4.0 255.255.255.224 10.0.0.1
Router-B(config)# ip route 192.168.4.64 255.255.255.192 10.0.0.1
Router-B(config)# ip route 192.168.4.128 255.255.255.192 10.0.0.1

Router-B(config)# end
Router-B# write memory
```

### 3.2.4 ACL cô lập phòng ban – Chi nhánh B [MỚI]

```bash
Router-B(config)# access-list 110 remark Chan ping giua cac phong ban CN-B

! IT (192.168.3.0/27) <-> Kho vận (192.168.3.64/26)
Router-B(config)# access-list 110 deny icmp 192.168.3.0 0.0.0.31 192.168.3.64 0.0.0.63
Router-B(config)# access-list 110 deny icmp 192.168.3.64 0.0.0.63 192.168.3.0 0.0.0.31

! IT (192.168.3.0/27) <-> Kỹ thuật (192.168.3.128/26)
Router-B(config)# access-list 110 deny icmp 192.168.3.0 0.0.0.31 192.168.3.128 0.0.0.63
Router-B(config)# access-list 110 deny icmp 192.168.3.128 0.0.0.63 192.168.3.0 0.0.0.31

! Kho vận (192.168.3.64/26) <-> Kỹ thuật (192.168.3.128/26)
Router-B(config)# access-list 110 deny icmp 192.168.3.64 0.0.0.63 192.168.3.128 0.0.0.63
Router-B(config)# access-list 110 deny icmp 192.168.3.128 0.0.0.63 192.168.3.64 0.0.0.63

Router-B(config)# access-list 110 permit ip any any
```

```bash
Router-B(config)# interface GigabitEthernet0/0
Router-B(config-if)# ip access-group 110 in
Router-B(config-if)# exit

Router-B(config)# interface GigabitEthernet0/1
Router-B(config-if)# ip access-group 110 in
Router-B(config-if)# exit

Router-B(config)# interface GigabitEthernet0/2
Router-B(config-if)# ip access-group 110 in
Router-B(config-if)# exit
```

### 3.2.5 SSH – Router-B

```bash
Router-B# configure terminal
Router-B(config)# ip domain-name logistics.local
Router-B(config)# username admin privilege 15 secret SSH@Logistics2024
Router-B(config)# username netops privilege 10 secret NetOps@123
Router-B(config)# crypto key generate rsa modulus 2048
Router-B(config)# ip ssh version 2
Router-B(config)# ip ssh time-out 60
Router-B(config)# ip ssh authentication-retries 3
Router-B(config)# line vty 0 4
Router-B(config-line)# transport input ssh
Router-B(config-line)# login local
Router-B(config-line)# exec-timeout 5 0
Router-B(config-line)# exit
```

### 3.2.6 NTP – Router-B (Client)

```bash
Router-B(config)# ntp server 10.0.0.1
Router-B(config)# clock timezone ICT 7
```

### 3.2.7 DHCP Relay – Router-B [CẬP NHẬT, hướng về Server-CORE thay vì Server-B1]

```bash
Router-B(config)# interface GigabitEthernet0/0
Router-B(config-if)# ip helper-address 192.168.1.66
Router-B(config-if)# exit

Router-B(config)# interface GigabitEthernet0/1
Router-B(config-if)# ip helper-address 192.168.1.66
Router-B(config-if)# exit

Router-B(config)# interface GigabitEthernet0/2
Router-B(config-if)# ip helper-address 192.168.1.66
Router-B(config-if)# exit

Router-B(config)# end
Router-B# write memory
```

---

## 3.3 Cấu hình Router-C (Chi nhánh C – Nhánh phụ 2 / Trạm trung chuyển) [HOÀN TOÀN MỚI]

### 3.3.1 Cấu hình cơ bản & bảo mật

```bash
Router> enable
Router# configure terminal
Router(config)# hostname Router-C

Router-C(config)# banner motd #
=========================================================
  TRUNG TAM LOGISTICS & KHO BAI THONG MINH - CHI NHANH C
  CANH BAO: Chi co nhan vien duoc uy quyen moi duoc truy cap!
  Moi truy cap trai phep se bi ghi lai va xu ly.
=========================================================
#

Router-C(config)# enable secret Logistics@2024
Router-C(config)# service password-encryption

Router-C(config)# line console 0
Router-C(config-line)# password Console@123
Router-C(config-line)# login
Router-C(config-line)# exit

Router-C(config)# line vty 0 4
Router-C(config-line)# transport input ssh
Router-C(config-line)# login local
Router-C(config-line)# exec-timeout 5 0
Router-C(config-line)# exit
```

### 3.3.2 Cấu hình Interface Router-C

```bash
! Interface kết nối Phòng IT/Điều hành (SW-C1)
Router-C(config)# interface GigabitEthernet0/0
Router-C(config-if)# description PB-DieuHanh-IT-CNC
Router-C(config-if)# ip address 192.168.4.1 255.255.255.224
Router-C(config-if)# no shutdown
Router-C(config-if)# exit

! Interface kết nối Phòng Vận chuyển (SW-C2)
Router-C(config)# interface GigabitEthernet0/1
Router-C(config-if)# description PB-VanChuyen-CNC
Router-C(config-if)# ip address 192.168.4.65 255.255.255.192
Router-C(config-if)# no shutdown
Router-C(config-if)# exit

! Interface kết nối Phòng Đóng gói (SW-C3)
Router-C(config)# interface GigabitEthernet0/2
Router-C(config-if)# description PB-DongGoi-CNC
Router-C(config-if)# ip address 192.168.4.129 255.255.255.192
Router-C(config-if)# no shutdown
Router-C(config-if)# exit

! Interface WAN kết nối Chi nhánh A (DTE)
Router-C(config)# interface Serial0/0/0
Router-C(config-if)# description WAN-Link-CNC-to-CNA
Router-C(config-if)# ip address 10.0.0.6 255.255.255.252
Router-C(config-if)# no shutdown
Router-C(config-if)# exit
```

### 3.3.3 Cấu hình Static Routing – Router-C (mọi tuyến đều qua Router-A – hub)

```bash
! Định tuyến tới các subnet Chi nhánh A (bao gồm subnet Server-CORE)
Router-C(config)# ip route 192.168.1.0 255.255.255.192 10.0.0.5
Router-C(config)# ip route 192.168.1.64 255.255.255.240 10.0.0.5
Router-C(config)# ip route 192.168.1.128 255.255.255.128 10.0.0.5
Router-C(config)# ip route 192.168.2.0 255.255.255.128 10.0.0.5

! Định tuyến tới các subnet Chi nhánh B – đi xuyên qua Router-A (hub)
Router-C(config)# ip route 192.168.3.0 255.255.255.224 10.0.0.5
Router-C(config)# ip route 192.168.3.64 255.255.255.192 10.0.0.5
Router-C(config)# ip route 192.168.3.128 255.255.255.192 10.0.0.5

Router-C(config)# end
Router-C# write memory
```

### 3.3.4 ACL cô lập phòng ban – Chi nhánh C

```bash
Router-C(config)# access-list 120 remark Chan ping giua cac phong ban CN-C

! IT (192.168.4.0/27) <-> Vận chuyển (192.168.4.64/26)
Router-C(config)# access-list 120 deny icmp 192.168.4.0 0.0.0.31 192.168.4.64 0.0.0.63
Router-C(config)# access-list 120 deny icmp 192.168.4.64 0.0.0.63 192.168.4.0 0.0.0.31

! IT (192.168.4.0/27) <-> Đóng gói (192.168.4.128/26)
Router-C(config)# access-list 120 deny icmp 192.168.4.0 0.0.0.31 192.168.4.128 0.0.0.63
Router-C(config)# access-list 120 deny icmp 192.168.4.128 0.0.0.63 192.168.4.0 0.0.0.31

! Vận chuyển (192.168.4.64/26) <-> Đóng gói (192.168.4.128/26)
Router-C(config)# access-list 120 deny icmp 192.168.4.64 0.0.0.63 192.168.4.128 0.0.0.63
Router-C(config)# access-list 120 deny icmp 192.168.4.128 0.0.0.63 192.168.4.64 0.0.0.63

Router-C(config)# access-list 120 permit ip any any
```

```bash
Router-C(config)# interface GigabitEthernet0/0
Router-C(config-if)# ip access-group 120 in
Router-C(config-if)# exit

Router-C(config)# interface GigabitEthernet0/1
Router-C(config-if)# ip access-group 120 in
Router-C(config-if)# exit

Router-C(config)# interface GigabitEthernet0/2
Router-C(config-if)# ip access-group 120 in
Router-C(config-if)# exit
```

### 3.3.5 SSH – Router-C

```bash
Router-C# configure terminal
Router-C(config)# ip domain-name logistics.local
Router-C(config)# username admin privilege 15 secret SSH@Logistics2024
Router-C(config)# username netops privilege 10 secret NetOps@123
Router-C(config)# crypto key generate rsa modulus 2048
Router-C(config)# ip ssh version 2
Router-C(config)# ip ssh time-out 60
Router-C(config)# ip ssh authentication-retries 3
Router-C(config)# line vty 0 4
Router-C(config-line)# transport input ssh
Router-C(config-line)# login local
Router-C(config-line)# exec-timeout 5 0
Router-C(config-line)# exit
```

### 3.3.6 NTP – Router-C (Client)

```bash
Router-C(config)# ntp server 10.0.0.5
Router-C(config)# clock timezone ICT 7
```

### 3.3.7 DHCP Relay – Router-C

```bash
Router-C(config)# interface GigabitEthernet0/0
Router-C(config-if)# ip helper-address 192.168.1.66
Router-C(config-if)# exit

Router-C(config)# interface GigabitEthernet0/1
Router-C(config-if)# ip helper-address 192.168.1.66
Router-C(config-if)# exit

Router-C(config)# interface GigabitEthernet0/2
Router-C(config-if)# ip helper-address 192.168.1.66
Router-C(config-if)# exit

Router-C(config)# end
Router-C# write memory
```

---

## 3.4 Cấu hình Switch Layer 2

### Switch CN A (SW-A1, SW-A2, SW-A3, SW-SERVER)

```bash
Switch> enable
Switch# configure terminal

! Ví dụ cho SW-A1 (thay tên tương ứng cho các switch khác)
Switch(config)# hostname SW-A1

SW-A1(config)# enable secret Logistics@2024
SW-A1(config)# service password-encryption

SW-A1(config)# line console 0
SW-A1(config-line)# password Console@123
SW-A1(config-line)# login
SW-A1(config-line)# exit

SW-A1(config)# line vty 0 4
SW-A1(config-line)# password VTY@123
SW-A1(config-line)# login
SW-A1(config-line)# exit

! Tắt các port không sử dụng (bảo mật)
SW-A1(config)# interface range FastEthernet0/25 - 24
SW-A1(config-if-range)# shutdown
SW-A1(config-if-range)# exit

SW-A1(config)# end
SW-A1# write memory
```

### Switch CN B (SW-B1, SW-B2, SW-B3) và CN C (SW-C1, SW-C2, SW-C3) – cấu hình tương tự

```bash
Switch(config)# hostname SW-B1
! ... thực hiện tương tự như SW-A1

Switch(config)# hostname SW-C1
! ... thực hiện tương tự như SW-A1 (áp dụng cho SW-C1, SW-C2, SW-C3)
```

> **Lưu ý:** Vì SW-BSRV (server farm CN B) đã bị loại bỏ, chỉ còn duy nhất **SW-SERVER** trong toàn hệ thống, đặt tại Chi nhánh A.

---

# PHẦN 4 – CẤU HÌNH SERVER-CORE (SERVER TẬP TRUNG DUY NHẤT)

> Theo yêu cầu #2 của giảng viên, toàn bộ dịch vụ mạng được gộp vào **một** server duy nhất: **Server-CORE**, đặt tại Chi nhánh A, phục vụ cho cả 3 chi nhánh (A, B, C) thông qua DHCP Relay và định tuyến.

## 4.1 Thông số IP – Server-CORE

| Thông số | Giá trị |
|----------|---------|
| Tên thiết bị | Server-CORE |
| IP Address | 192.168.1.66 |
| Subnet Mask | 255.255.255.240 (/28) |
| Default Gateway | 192.168.1.65 |
| DNS Server | 192.168.1.66 (tự phân giải) |

**Bật tất cả dịch vụ trong tab Services:**

```text
DHCP      ON
DNS       ON
HTTP      ON
HTTPS     ON
FTP       ON
EMAIL     ON
NTP       ON
```

---

## 4.2 Dịch vụ DHCP (Server-CORE) – phục vụ cả 3 chi nhánh qua DHCP Relay

**Tạo DHCP Pool cho từng phòng ban / từng chi nhánh:**

| Pool Name | Chi nhánh | Default Gateway | DNS Server | Start IP | Max Users | Subnet Mask |
|-----------|-----------|----------------|------------|----------|-----------|-------------|
| POOL-A1-IT | A | 192.168.1.1 | 192.168.1.66 | 192.168.1.10 | 50 | 255.255.255.192 |
| POOL-A2-KD | A | 192.168.1.129 | 192.168.1.66 | 192.168.1.135 | 118 | 255.255.255.128 |
| POOL-A3-KV | A | 192.168.2.1 | 192.168.1.66 | 192.168.2.10 | 115 | 255.255.255.128 |
| POOL-B1-IT | B | 192.168.3.1 | 192.168.1.66 | 192.168.3.6 | 24 | 255.255.255.224 |
| POOL-B2-KV | B | 192.168.3.65 | 192.168.1.66 | 192.168.3.70 | 55 | 255.255.255.192 |
| POOL-B3-KT | B | 192.168.3.129 | 192.168.1.66 | 192.168.3.135 | 55 | 255.255.255.192 |
| POOL-C1-IT | C | 192.168.4.1 | 192.168.1.66 | 192.168.4.6 | 24 | 255.255.255.224 |
| POOL-C2-VC | C | 192.168.4.65 | 192.168.1.66 | 192.168.4.70 | 55 | 255.255.255.192 |
| POOL-C3-DG | C | 192.168.4.129 | 192.168.1.66 | 192.168.4.135 | 55 | 255.255.255.192 |

> **Lưu ý:** Không cần cấu hình `ip dhcp pool` trên Router nữa – Router chỉ đóng vai trò **DHCP Relay Agent** (xem mục 3.1.7 / 3.2.7 / 3.3.7), toàn bộ pool được quản lý tập trung tại Server-CORE để đúng yêu cầu "chỉ dùng 1 server".

---

## 4.3 Dịch vụ DNS (Server-CORE)

**Bảng DNS Records:**

| Tên miền nội bộ | Loại Record | Địa chỉ IP | Mô tả |
|-----------------|-------------|------------|-------|
| `logistics.local` | A Record | 192.168.1.66 | Web Server chính |
| `www.logistics.local` | A Record | 192.168.1.66 | Web Server (alias) |
| `ftp.logistics.local` | A Record | 192.168.1.66 | FTP Server |
| `mail.logistics.local` | A Record | 192.168.1.66 | Email Server |
| `server-core.logistics.local` | A Record | 192.168.1.66 | Server tập trung |
| `router-a.logistics.local` | A Record | 192.168.1.1 | Router CN A (hub) |
| `router-b.logistics.local` | A Record | 192.168.3.1 | Router CN B |
| `router-c.logistics.local` | A Record | 192.168.4.1 | Router CN C |

**Các bước thực hiện trong Packet Tracer:**
1. Mở Server-CORE → tab **Services** → chọn **DNS**
2. Bật **DNS Service: ON**
3. Nhập từng record vào ô **Name** và **Address**, chọn loại **A Record**, nhấn **Add**

---

## 4.4 Dịch vụ HTTP/HTTPS (Server-CORE)

> Vào **Services → HTTP**: Bật **HTTP ON** và **HTTPS ON**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Trung tâm Logistics & Kho bãi thông minh</title>
</head>
<body>
  <h1>Chào mừng đến Hệ thống Nội bộ Logistics</h1>
  <p>Portal quản lý kho bãi và vận chuyển nội bộ - 3 chi nhánh A, B, C.</p>
  <ul>
    <li>Quản lý đơn hàng</li>
    <li>Theo dõi hàng hóa</li>
    <li>Báo cáo vận hành</li>
  </ul>
</body>
</html>
```

**Kiểm tra từ PC bất kỳ (A, B, C):** Mở Web Browser → `http://logistics.local` hoặc `http://192.168.1.66`

---

## 4.5 Dịch vụ Email – SMTP + POP3 (Server-CORE)

> Vào **Services → EMAIL**: Bật **SMTP ON** và **POP3 ON**, Domain Name: `logistics.local`

| Username | Password | Mô tả |
|----------|----------|-------|
| admin | Admin@123 | Quản trị viên hệ thống |
| it-dept | IT@123 | Phòng IT (cả 3 chi nhánh) |
| sales | Sales@123 | Phòng Kinh doanh – CN A |
| warehouse | WH@123 | Phòng Kho vận – CN A/B |
| transport | Transport@123 | Phòng Vận chuyển – CN C |
| packing | Packing@123 | Phòng Đóng gói – CN C |

**Cấu hình Email Client trên PC:** Incoming (POP3) / Outgoing (SMTP) Server đều là `192.168.1.66` hoặc `mail.logistics.local`.

---

## 4.6 Dịch vụ FTP (Server-CORE)

> Vào **Services → FTP**: Bật **ON**

| Username | Password | Quyền truy cập |
|----------|----------|----------------|
| ftp_logistics | FTP@Logistics | Read, Write, Delete, Rename, List |
| ftp_readonly | ReadOnly@123 | Read, List |
| ftp_warehouse | WH_FTP@123 | Read, Write, List |

```bash
! Truy cập FTP từ PC bất kỳ
ftp 192.168.1.66
! hoặc
ftp ftp.logistics.local

Username: ftp_logistics
Password: FTP@Logistics

ftp> dir
ftp> get file.txt
ftp> put file.txt
ftp> quit
```

---

## 4.7 Dịch vụ NTP (Server-CORE)

> Vào **Services → NTP**: Bật **ON**, đồng bộ thời gian theo Router-A (NTP Master, stratum 3)

```bash
Router-A# show ntp status
Router-A# show clock
Router-B# show ntp associations
Router-C# show ntp associations
```

---

## 4.8 Tóm tắt Dịch vụ trên từng Thiết bị

| Thiết bị | IP | DHCP | DNS | HTTP/HTTPS | FTP | Email | SSH | NTP |
|----------|-----|------|-----|-----------|-----|-------|-----|-----|
| Router-A | 10.0.0.1 / 10.0.0.5 / 192.168.x.x | ✅ Relay | – | – | – | – | ✅ | ✅ Master |
| Router-B | 10.0.0.2 / 192.168.3.x | ✅ Relay | – | – | – | – | ✅ | ✅ Client |
| Router-C | 10.0.0.6 / 192.168.4.x | ✅ Relay | – | – | – | – | ✅ | ✅ Client |
| **Server-CORE** | 192.168.1.66 | ✅ Server | ✅ | ✅ | ✅ | ✅ SMTP/POP3 | – | ✅ |

---

# PHẦN 5 – KIỂM TRA CÔ LẬP PHÒNG BAN (ACL) – KẾT QUẢ MONG ĐỢI

| Test | Kết quả mong đợi |
|------|------------------|
| IT-A → IT-A (cùng phòng ban) | ✅ Allowed |
| IT-A → Kinh doanh-A | ❌ Blocked |
| IT-A → Kho vận-A | ❌ Blocked |
| Kinh doanh-A → Kho vận-A | ❌ Blocked |
| IT-B → Kho vận-B | ❌ Blocked |
| IT-B → Kỹ thuật-B | ❌ Blocked |
| Kho vận-B → Kỹ thuật-B | ❌ Blocked |
| IT-C → Vận chuyển-C | ❌ Blocked |
| IT-C → Đóng gói-C | ❌ Blocked |
| Vận chuyển-C → Đóng gói-C | ❌ Blocked |
| Tất cả phòng ban (A, B, C) → Server-CORE | ✅ Allowed |
| Chi nhánh A ↔ Chi nhánh B (qua WAN) | ✅ Allowed (ACL chỉ chặn nội bộ cùng chi nhánh) |
| Chi nhánh A ↔ Chi nhánh C (qua WAN) | ✅ Allowed |
| Chi nhánh B ↔ Chi nhánh C (trung chuyển qua Router-A) | ✅ Allowed |

```bash
! ===== Kiểm tra ACL =====
Router-A# show access-lists 100
Router-B# show access-lists 110
Router-C# show access-lists 120
Router-A# show ip interface GigabitEthernet0/0

! Ping thử từ PC IT-A tới PC Kinh doanh-A -> phải Request timed out
! Ping thử từ PC bất kỳ tới Server-CORE (192.168.1.66) -> phải Reply
```

---

# PHẦN 6 – LỆNH KIỂM TRA TOÀN HỆ THỐNG

```bash
! ===== Kiểm tra DHCP =====
! Từ PC: Desktop → IP Configuration → chọn DHCP
Router-A# show ip dhcp binding
Router-B# show ip route
Router-C# show ip route

! ===== Kiểm tra DNS =====
ping logistics.local
nslookup logistics.local

! ===== Kiểm tra HTTP =====
! Web Browser → http://logistics.local

! ===== Kiểm tra FTP =====
ftp ftp.logistics.local

! ===== Kiểm tra Email =====
! Desktop → Email → gửi thử tới admin@logistics.local

! ===== Kiểm tra SSH =====
ssh -l admin 192.168.1.1
Router-B# ssh -l admin 192.168.1.1
Router-C# ssh -l admin 192.168.1.1

! ===== Kiểm tra NTP =====
Router-A# show ntp status
Router-B# show ntp associations
Router-C# show ntp associations

! ===== Kiểm tra kết nối liên chi nhánh (hub-and-spoke) =====
Router-A# ping 10.0.0.2
Router-A# ping 10.0.0.6
Router-B# ping 192.168.4.1
Router-C# ping 192.168.3.1

! ===== Kiểm tra routing =====
Router-A# show ip route
Router-B# show ip route
Router-C# show ip route
```

---

# PHỤ LỤC – BẢNG VLSM TỔNG HỢP

## Chi nhánh A – Trụ sở chính (Nhánh chính)

| Subnet | Phòng ban | Host cần | Mask | Network | Gateway | Usable Range | Broadcast |
|--------|-----------|----------|------|---------|---------|--------------|-----------|
| A1 | IT & Điều hành | 30 | /26 | 192.168.1.0 | 192.168.1.1 | .2 – .62 | 192.168.1.63 |
| A-SRV | Server-CORE | 10 | /28 | 192.168.1.64 | 192.168.1.65 | .66 – .78 | 192.168.1.79 |
| A2 | Kinh doanh | 80 | /25 | 192.168.1.128 | 192.168.1.129 | .130 – .254 | 192.168.1.255 |
| A3 | Kho vận | 70 | /25 | 192.168.2.0 | 192.168.2.1 | .2 – .126 | 192.168.2.127 |

## Chi nhánh B – Nhánh phụ 1 / Kho vệ tinh

| Subnet | Phòng ban | Host cần | Mask | Network | Gateway | Usable Range | Broadcast |
|--------|-----------|----------|------|---------|---------|--------------|-----------|
| B1 | IT & Điều hành | 20 | /27 | 192.168.3.0 | 192.168.3.1 | .2 – .30 | 192.168.3.31 |
| B2 | Kho vận | 60 | /26 | 192.168.3.64 | 192.168.3.65 | .66 – .126 | 192.168.3.127 |
| B3 | Kỹ thuật | 40 | /26 | 192.168.3.128 | 192.168.3.129 | .130 – .190 | 192.168.3.191 |
| (B-SRV) | *Đã loại bỏ – dự phòng mở rộng* | – | /29 | 192.168.3.192 | – | – | 192.168.3.199 |

## Chi nhánh C – Nhánh phụ 2 / Trạm trung chuyển [MỚI]

| Subnet | Phòng ban | Host cần | Mask | Network | Gateway | Usable Range | Broadcast |
|--------|-----------|----------|------|---------|---------|--------------|-----------|
| C1 | IT & Điều hành | 20 | /27 | 192.168.4.0 | 192.168.4.1 | .2 – .30 | 192.168.4.31 |
| C2 | Vận chuyển | 50 | /26 | 192.168.4.64 | 192.168.4.65 | .66 – .126 | 192.168.4.127 |
| C3 | Đóng gói | 30 | /26 | 192.168.4.128 | 192.168.4.129 | .130 – .190 | 192.168.4.191 |
| (Dự phòng) | Mở rộng tương lai | – | /26 | 192.168.4.192 | – | – | 192.168.4.255 |

## Kết nối WAN (Hub-and-Spoke)

| Liên kết | Mask | Network | Router-A (Hub) | Router phụ | Broadcast |
|----------|------|---------|----------------|-------------|-----------|
| WAN A–B | /30 | 10.0.0.0 | 10.0.0.1 (DCE) | 10.0.0.2 (DTE, Router-B) | 10.0.0.3 |
| WAN A–C | /30 | 10.0.0.4 | 10.0.0.5 (DCE) | 10.0.0.6 (DTE, Router-C) | 10.0.0.7 |

---

# TÓM TẮT THIẾT KẾ CUỐI CÙNG

✔ Cô lập phòng ban bằng ACL tại cả 3 chi nhánh (A, B, C) – chặn ping liên-phòng-ban, vẫn cho phép truy cập Server
✔ Server tập trung duy nhất – **Server-CORE** (gộp DHCP, DNS, HTTP, HTTPS, FTP, Email, NTP)
✔ Router kết nối Server qua Switch (SW-SERVER) – đúng chuẩn doanh nghiệp
✔ DHCP Relay (ip helper-address) cho cả 3 chi nhánh, hướng về Server-CORE
✔ Mô hình mở rộng **hub-and-spoke**: 1 nhánh chính (Chi nhánh A) + 2 nhánh phụ (Chi nhánh B, Chi nhánh C)
✔ Định tuyến tĩnh xuyên chi nhánh (B ↔ C) trung chuyển qua Router-A

---

*Tài liệu được tổng hợp và hoàn thiện cho môn NWC204 – SU26*
*Mô hình: Trung tâm Logistics & Kho bãi thông minh – 1 nhánh chính + 2 nhánh phụ*
*Phần mềm mô phỏng: Cisco Packet Tracer*