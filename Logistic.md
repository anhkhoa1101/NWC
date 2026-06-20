# TÀI LIỆU HỆ THỐNG MẠNG – TRUNG TÂM LOGISTICS & KHO BÃI THÔNG MINH
### Môn: NWC204 – SU26 | Phần mềm: Cisco Packet Tracer

> **📝 Cập nhật trong phiên bản này:**
> 1. **Gộp server:** Chi nhánh A từ 3 server (Server-A1/A2/A3) → **1 server duy nhất (Server-A)** chạy đồng thời DHCP/DNS/NTP/HTTP/Email/FTP. Chi nhánh B vẫn giữ 1 server (Server-B) như cũ.
> 2. **ACL giới hạn ping:** Thêm Extended ACL (mục **3.4**) trên Router-A và Router-B để mỗi phòng ban chỉ ping được trong nội bộ, không ping được sang phòng ban/chi nhánh khác. Các dịch vụ DHCP/DNS/HTTP/FTP/Email vẫn hoạt động bình thường.
> 3. **Giải thích lỗi Serial DCE/DTE:** Thêm mục **3.1.2.1** giải thích lỗi thường gặp khi đi dây Serial (clock rate phải đặt đúng đầu DCE) kèm cách kiểm tra và khắc phục.

---

# PHẦN 1 – BẢNG KẾT NỐI THIẾT BỊ

## 1.1 Bảng Kết nối: Thiết bị → Thiết bị

| STT | Thiết bị Nguồn | Thiết bị Đích | Dây Kết nối | Cổng Nguồn | Cổng Đích |
|-----|---------------|--------------|-------------|------------|-----------|
| 1 | Router-A | SW-A1 (IT/Điều hành CN A) | Copper Straight-Through | GigabitEthernet0/0 | FastEthernet0/1 |
| 2 | Router-A | SW-A2 (Kinh doanh CN A) | Copper Straight-Through | GigabitEthernet0/1 | FastEthernet0/1 |
| 3 | Router-A | SW-A3 (Kho vận CN A) | Copper Straight-Through | GigabitEthernet0/2 | FastEthernet0/1 |
| 4 | Router-A | SW-ASRV (Server Farm A) | Copper Straight-Through | GigabitEthernet0/3 | FastEthernet0/1 |
| 5 | Router-A | Router-B | Serial DCE/DTE | Serial0/0/0 | Serial0/0/0 |
| 6 | Router-B | SW-B1 (IT/Điều hành CN B) | Copper Straight-Through | GigabitEthernet0/0 | FastEthernet0/1 |
| 7 | Router-B | SW-B2 (Kho vận CN B) | Copper Straight-Through | GigabitEthernet0/1 | FastEthernet0/1 |
| 8 | Router-B | SW-B3 (Kỹ thuật CN B) | Copper Straight-Through | GigabitEthernet0/2 | FastEthernet0/1 |
| 9 | Router-B | SW-BSRV (Server Farm B) | Copper Straight-Through | GigabitEthernet0/3 | FastEthernet0/1 |
| 10 | SW-A1 | PC-A1-01 → PC-A1-30 | Copper Straight-Through | FastEthernet0/2 → Fa0/31 | FastEthernet0 |
| 11 | SW-A2 | PC-A2-01 → PC-A2-80 | Copper Straight-Through | FastEthernet0/2 → Fa0/81 | FastEthernet0 |
| 12 | SW-A3 | PC-A3-01 → PC-A3-70 | Copper Straight-Through | FastEthernet0/2 → Fa0/71 | FastEthernet0 |
| 13 | SW-ASRV | Server-A (CORE: DHCP/DNS/NTP/HTTP/Email/FTP) | Copper Straight-Through | FastEthernet0/2 | FastEthernet0 |
| 14 | SW-B1 | PC-B1-01 → PC-B1-20 | Copper Straight-Through | FastEthernet0/2 → Fa0/21 | FastEthernet0 |
| 15 | SW-B2 | PC-B2-01 → PC-B2-60 | Copper Straight-Through | FastEthernet0/2 → Fa0/61 | FastEthernet0 |
| 16 | SW-B3 | PC-B3-01 → PC-B3-40 | Copper Straight-Through | FastEthernet0/2 → Fa0/41 | FastEthernet0 |
| 17 | SW-BSRV | Server-B (DHCP CN B) | Copper Straight-Through | FastEthernet0/2 | FastEthernet0 |
| 18 | SW-A1 | Printer-A1 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 19 | SW-A2 | Printer-A2 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 20 | SW-A3 | Printer-A3 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 21 | SW-B1 | Printer-B1 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |
| 22 | SW-B2 | Printer-B2 | Copper Straight-Through | FastEthernet0/24 | FastEthernet0 |

> **Ghi chú:** Kết nối Router-A ↔ Router-B sử dụng cáp Serial (DCE tại Router-A với clock rate 64000).
>
> ⚠️ **Lưu ý quan trọng về cáp Serial DCE/DTE:** Trong Cisco Packet Tracer, cáp Serial có 2 đầu khác nhau – một đầu là **DCE** (đầu có biểu tượng đồng hồ ⏱, đóng vai trò cấp xung clock) và đầu còn lại là **DTE** (đầu nhận xung clock). Lệnh `clock rate` **chỉ được phép cấu hình trên đầu DCE**; nếu cấu hình nhầm trên đầu DTE, Router sẽ báo lỗi `"This command applies only to DCE interfaces"` và đường truyền sẽ ở trạng thái **"up/down"** (line protocol down) dù hai đầu đã `no shutdown`. Xem mục **3.1.2.1** để biết cách kiểm tra và khắc phục lỗi này.

---

# PHẦN 2 – SƠ ĐỒ MẠNG (NETWORK TOPOLOGY)

## 2.1 Sơ đồ Logic Topology

```
                    ╔══════════════════════════════════════════════════╗
                    ║       CHI NHÁNH A – TRỤ SỞ CHÍNH                 ║
                    ║                                                  ║
                    ║            ┌─────────────────────┐               ║
                    ║            │      ROUTER-A        │              ║
                    ║            │  Se0/0/0: 10.0.0.1   │              ║
                    ║            │  Gi0/0: 192.168.1.1  │              ║
                    ║            │  Gi0/1: 192.168.1.129│              ║
                    ║            │  Gi0/2: 192.168.2.1  │              ║
                    ║            │  Gi0/3: 192.168.1.65 │              ║
                    ║            └──┬──────┬──────┬──┬──┘              ║
                    ║         Gi0/0 │ Gi0/1│ Gi0/2│  │ Gi0/3           ║
                    ║               │      │      │  │                 ║
                    ║    ┌──────────┘  ┌───┘  ┌──┘  └───────────┐      ║
                    ║    │             │      │                  │     ║
                    ║  ┌─▼──────────┐ │  ┌───▼────────┐  ┌──────▼──┐   ║
                    ║  │   SW-A1    │ │  │   SW-A3    │  │ SW-ASRV │   ║
                    ║  │IT/Điều hành│ │  │  Kho vận   │  │ Farm A  │   ║
                    ║  │192.168.1.0 │ │  │192.168.2.0 │  │192.168.1│   ║
                    ║  │    /26     │ │  │    /25     │  │  .64/28 │   ║
                    ║  └────────────┘ │  └────────────┘  └────┬────┘   ║
                    ║  30 PCs+Printer │  70 PCs+Printer        │       ║
                    ║               ┌─▼──────────┐       ┌────▼──────┐ ║
                    ║               │   SW-A2    │       │ Server-A  │ ║
                    ║               │ Kinh doanh │       │192.168.1.66│║
                    ║               │192.168.1.128│      │ DHCP/DNS/ │ ║
                    ║               │    /25     │       │   NTP    │ ║
                    ║               └────────────┘       │HTTP/Email/│ ║
                    ║               80 PCs+Printer        │    FTP    │ ║
                    ║                                    └───────────┘ ║
                    ╚════════════════════════╦═════════════════════════╝
                                             │
                                   Serial WAN Link
                                   10.0.0.0/30
                                 (DCE) 10.0.0.1 ◄─► 10.0.0.2 (DTE)
                                             │
                    ╔════════════════════════╩═════════════════════════╗
                    ║       CHI NHÁNH B – KHO VỆ TINH                 ║
                    ║                                                  ║
                    ║            ┌─────────────────────┐              ║
                    ║            │      ROUTER-B        │              ║
                    ║            │  Se0/0/0: 10.0.0.2   │              ║
                    ║            │  Gi0/0: 192.168.3.1  │              ║
                    ║            │  Gi0/1: 192.168.3.65 │              ║
                    ║            │  Gi0/2: 192.168.3.129│              ║
                    ║            │  Gi0/3: 192.168.3.193│              ║
                    ║            └──┬──────┬──────┬──┬──┘              ║
                    ║         Gi0/0 │ Gi0/1│ Gi0/2│  │ Gi0/3           ║
                    ║               │      │      │  │                 ║
                    ║    ┌──────────┘  ┌───┘  ┌──┘  └───────────┐     ║
                    ║    │             │      │                  │     ║
                    ║  ┌─▼──────────┐ │  ┌───▼────────┐  ┌──────▼──┐  ║
                    ║  │   SW-B1    │ │  │   SW-B3    │  │ SW-BSRV │  ║
                    ║  │IT/Điều hành│ │  │  Kỹ thuật  │  │ Farm B  │  ║
                    ║  │192.168.3.0 │ │  │192.168.3.128│ │192.168.3│  ║
                    ║  │    /27     │ │  │    /26     │  │ .192/29 │  ║
                    ║  └────────────┘ │  └────────────┘  └────┬────┘  ║
                    ║  20 PCs+Printer │  40 PCs             ┌──▼──────┐║
                    ║               ┌─▼──────────┐          │Server-B ││
                    ║               │   SW-B2    │          │192.168.3│║
                    ║               │  Kho vận   │          │  .194   ││
                    ║               │192.168.3.64│          │  DHCP   ││
                    ║               │    /26     │          └─────────┘║
                    ║               └────────────┘                     ║
                    ║               60 PCs+Printer                     ║
                    ╚══════════════════════════════════════════════════╝
```

## 2.2 Bảng Tóm tắt Địa chỉ IP Toàn hệ thống

| Thiết bị | Interface | IP Address | Subnet Mask | Ghi chú |
|----------|-----------|-----------|-------------|---------|
| Router-A | Gi0/0 | 192.168.1.1 | 255.255.255.192 (/26) | Gateway PB IT – CN A |
| Router-A | Gi0/1 | 192.168.1.129 | 255.255.255.128 (/25) | Gateway PB Kinh doanh – CN A |
| Router-A | Gi0/2 | 192.168.2.1 | 255.255.255.128 (/25) | Gateway PB Kho vận – CN A |
| Router-A | Gi0/3 | 192.168.1.65 | 255.255.255.240 (/28) | Gateway Server Farm A |
| Router-A | Se0/0/0 | 10.0.0.1 | 255.255.255.252 (/30) | WAN link tới CN B (DCE) |
| Router-B | Gi0/0 | 192.168.3.1 | 255.255.255.224 (/27) | Gateway PB IT – CN B |
| Router-B | Gi0/1 | 192.168.3.65 | 255.255.255.192 (/26) | Gateway PB Kho vận – CN B |
| Router-B | Gi0/2 | 192.168.3.129 | 255.255.255.192 (/26) | Gateway PB Kỹ thuật – CN B |
| Router-B | Gi0/3 | 192.168.3.193 | 255.255.255.248 (/29) | Gateway Server Farm B |
| Router-B | Se0/0/0 | 10.0.0.2 | 255.255.255.252 (/30) | WAN link tới CN A (DTE) |
| Server-A | NIC | 192.168.1.66 | 255.255.255.240 (/28) | 1 server duy nhất CN A: DHCP + DNS + NTP + HTTP/HTTPS + Email + FTP |
| Server-B | NIC | 192.168.3.194 | 255.255.255.248 (/29) | 1 server duy nhất CN B: DHCP (có thể mở rộng thêm dịch vụ khác nếu cần) |

---

# PHẦN 3 – DANH SÁCH LỆNH CẤU HÌNH

## 3.1 Cấu hình Router-A (Chi nhánh A – Trụ sở chính)

### 3.1.1 Cấu hình cơ bản & bảo mật

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

! Bảo mật VTY
Router-A(config)# line vty 0 4
Router-A(config-line)# transport input ssh
Router-A(config-line)# login local
Router-A(config-line)# exec-timeout 5 0
Router-A(config-line)# exit

! Mã hóa tất cả mật khẩu
Router-A(config)# service password-encryption
```

### 3.1.2 Cấu hình Interface Router-A

```bash
! Interface kết nối Phòng Điều hành & IT (SW-A1)
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

! Interface kết nối Server Farm A
Router-A(config)# interface GigabitEthernet0/3
Router-A(config-if)# description ServerFarm-CNA
Router-A(config-if)# ip address 192.168.1.65 255.255.255.240
Router-A(config-if)# no shutdown
Router-A(config-if)# exit

! Interface WAN kết nối Chi nhánh B (DCE – cấp clock)
Router-A(config)# interface Serial0/0/0
Router-A(config-if)# description WAN-Link-CNA-to-CNB
Router-A(config-if)# ip address 10.0.0.1 255.255.255.252
Router-A(config-if)# clock rate 64000
Router-A(config-if)# bandwidth 64
Router-A(config-if)# no shutdown
Router-A(config-if)# exit
```

### 3.1.2.1 ⚠️ Lưu ý & Khắc phục lỗi Serial DCE/DTE

Đây là lỗi phổ biến nhất khi sinh viên đi dây Serial trong Packet Tracer, không liên quan đến địa chỉ IP mà liên quan đến **đầu cáp vật lý**:

**Nguyên nhân lỗi:**
- Cáp Serial trong Packet Tracer có 2 đầu khác nhau: đầu **DCE** (có ký hiệu đồng hồ, chịu trách nhiệm cấp xung clock) và đầu **DTE** (nhận xung clock từ DCE).
- Lệnh `clock rate 64000` **chỉ chạy được trên đầu DCE**. Nếu sinh viên cắm cáp ngược (đầu DCE lại nằm ở Router-B) rồi gõ `clock rate` trên Router-A, Router sẽ trả về lỗi:
  ```
  Router-A(config-if)# clock rate 64000
  This command applies only to DCE interfaces
  ```
- Nếu không phát hiện và không gõ `clock rate` ở đúng đầu DCE, hai Router vẫn `no shutdown` nhưng cổng Serial sẽ ở trạng thái **`Serial0/0/0 is up, line protocol is down`** — tức là không có lỗi địa chỉ IP, nhưng đường truyền không hoạt động vì thiếu xung clock đồng bộ.

**Cách kiểm tra đầu nào là DCE:**
```bash
Router-A# show controllers serial0/0/0
! Dòng đầu tiên sẽ hiện "DCE V.35" hoặc "DTE V.35"
```

**Cách khắc phục:**
1. Dùng `show controllers serial0/0/0` trên cả hai Router để xác định Router nào đang giữ đầu DCE.
2. Chỉ cấu hình `clock rate 64000` trên Router đang giữ đầu DCE (theo tài liệu này là Router-A).
3. Trên đầu DTE (Router-B), **không** gõ `clock rate` — chỉ cần đặt IP và `no shutdown`.
4. Kiểm tra lại trạng thái đường truyền:
```bash
Router-A# show ip interface brief
! Kết quả mong đợi: Serial0/0/0  ...  up      up
Router-A# show interfaces serial0/0/0
! Kiểm tra dòng: Serial0/0/0 is up, line protocol is up
```
> Lưu ý: địa chỉ IP `10.0.0.1/30` và `10.0.0.2/30` trong tài liệu là đúng (đây là 2 host khả dụng duy nhất của mạng /30 `10.0.0.0/30`), nên lỗi giảng viên đề cập gần như chắc chắn là lỗi **xung clock DCE/DTE** ở trên, không phải lỗi đánh sai địa chỉ IP.

### 3.1.3 Cấu hình Static Routing – Router-A

```bash
! Static route tới tất cả subnet Chi nhánh B
Router-A(config)# ip route 192.168.3.0 255.255.255.224 10.0.0.2
Router-A(config)# ip route 192.168.3.64 255.255.255.192 10.0.0.2
Router-A(config)# ip route 192.168.3.128 255.255.255.192 10.0.0.2
Router-A(config)# ip route 192.168.3.192 255.255.255.248 10.0.0.2

Router-A(config)# end
Router-A# write memory
```

### 3.1.4 Cấu hình SSH – Router-A

```bash
Router-A# configure terminal

! Tạo domain name (bắt buộc trước khi generate key)
Router-A(config)# ip domain-name logistics.local

! Tạo local user cho SSH
Router-A(config)# username admin privilege 15 secret SSH@Logistics2024
Router-A(config)# username netops privilege 10 secret NetOps@123

! Generate RSA key 2048 bit
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
Router-A(config-line)# exit
```

### 3.1.5 Cấu hình NTP – Router-A

```bash
! Router-A làm NTP Master (stratum 3 cho nội bộ)
Router-A(config)# ntp master 3
Router-A(config)# ntp update-calendar
```

### 3.1.6 Cấu hình DHCP – Router-A

```bash
! Loại trừ IP Gateway và Server khỏi pool DHCP
Router-A(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
Router-A(config)# ip dhcp excluded-address 192.168.1.65 192.168.1.75
Router-A(config)# ip dhcp excluded-address 192.168.1.129 192.168.1.135
Router-A(config)# ip dhcp excluded-address 192.168.2.1 192.168.2.5

! DHCP Pool cho Phòng Điều hành & IT (CN A) – Subnet A1
Router-A(config)# ip dhcp pool POOL-A1-IT
Router-A(dhcp-config)# network 192.168.1.0 255.255.255.192
Router-A(dhcp-config)# default-router 192.168.1.1
Router-A(dhcp-config)# dns-server 192.168.1.66
Router-A(dhcp-config)# domain-name logistics.local
Router-A(dhcp-config)# lease 1
Router-A(dhcp-config)# exit

! DHCP Pool cho Phòng Kinh doanh (CN A) – Subnet A2
Router-A(config)# ip dhcp pool POOL-A2-KD
Router-A(dhcp-config)# network 192.168.1.128 255.255.255.128
Router-A(dhcp-config)# default-router 192.168.1.129
Router-A(dhcp-config)# dns-server 192.168.1.66
Router-A(dhcp-config)# domain-name logistics.local
Router-A(dhcp-config)# lease 1
Router-A(dhcp-config)# exit

! DHCP Pool cho Phòng Kho vận (CN A) – Subnet A3
Router-A(config)# ip dhcp pool POOL-A3-KV
Router-A(dhcp-config)# network 192.168.2.0 255.255.255.128
Router-A(dhcp-config)# default-router 192.168.2.1
Router-A(dhcp-config)# dns-server 192.168.1.66
Router-A(dhcp-config)# domain-name logistics.local
Router-A(dhcp-config)# lease 1
Router-A(dhcp-config)# exit
```

### 3.1.7 DHCP Relay (IP Helper-Address) – Router-A

```bash
! Nếu dùng Server-A (192.168.1.66) làm DHCP Server riêng
! Thêm helper-address trên mỗi interface của Router-A

Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# ip helper-address 192.168.1.66
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# ip helper-address 192.168.1.66
Router-A(config-if)# exit
```

---

## 3.2 Cấu hình Router-B (Chi nhánh B – Kho vệ tinh)

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
! Interface kết nối Phòng Điều hành & IT (SW-B1)
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

! Interface kết nối Server Farm B
Router-B(config)# interface GigabitEthernet0/3
Router-B(config-if)# description ServerFarm-CNB
Router-B(config-if)# ip address 192.168.3.193 255.255.255.248
Router-B(config-if)# no shutdown
Router-B(config-if)# exit

! Interface WAN kết nối Chi nhánh A (DTE – KHÔNG cấu hình clock rate ở đầu này)
Router-B(config)# interface Serial0/0/0
Router-B(config-if)# description WAN-Link-CNB-to-CNA
Router-B(config-if)# ip address 10.0.0.2 255.255.255.252
Router-B(config-if)# bandwidth 64
Router-B(config-if)# no shutdown
Router-B(config-if)# exit
```

### 3.2.3 Cấu hình Static Routing – Router-B

```bash
! Static route tới tất cả subnet Chi nhánh A
Router-B(config)# ip route 192.168.1.0 255.255.255.192 10.0.0.1
Router-B(config)# ip route 192.168.1.64 255.255.255.240 10.0.0.1
Router-B(config)# ip route 192.168.1.128 255.255.255.128 10.0.0.1
Router-B(config)# ip route 192.168.2.0 255.255.255.128 10.0.0.1

Router-B(config)# end
Router-B# write memory
```

### 3.2.4 Cấu hình SSH – Router-B

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

### 3.2.5 Cấu hình NTP – Router-B

```bash
! Router-B đồng bộ thời gian từ Router-A qua WAN
Router-B(config)# ntp server 10.0.0.1
```

---

## 3.3 Cấu hình Switch Layer 2

### Switch CN A (SW-A1, SW-A2, SW-A3, SW-ASRV)

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

### Switch CN B (SW-B1, SW-B2, SW-B3, SW-BSRV) – cấu hình tương tự

```bash
Switch(config)# hostname SW-B1
! ... (thực hiện tương tự như SW-A1)
```

---

# PHẦN 3.4 – ACCESS CONTROL LIST (ACL): CHỈ CHO PHÉP PING NỘI BỘ PHÒNG BAN

## 3.4.1 Yêu cầu

Mỗi phòng ban (subnet) **chỉ được ping (ICMP) trong nội bộ phòng ban của mình**, **không được ping sang phòng ban khác** — kể cả phòng ban khác trong cùng chi nhánh hoặc phòng ban ở chi nhánh đối diện. Các dịch vụ khác (DHCP, DNS, HTTP, FTP, Email) vẫn hoạt động bình thường giữa các phòng ban vì các dịch vụ này dùng TCP/UDP, không bị ACL chặn (ACL chỉ chặn giao thức ICMP).

Vì mô hình **không dùng VLAN/Switch Layer 3** (mỗi phòng ban là 1 cổng riêng trên Router), nên mọi gói tin đi giữa 2 phòng ban bắt buộc phải đi qua Router → đây chính là vị trí lý tưởng để áp **Extended ACL** chặn ICMP liên-phòng ban.

## 3.4.2 ACL trên Router-A

```bash
Router-A# configure terminal

! ACL áp cho PB IT/Điều hành (A1 – 192.168.1.0/26) – chặn ping đến tất cả phòng ban khác
Router-A(config)# access-list 101 deny icmp 192.168.1.0 0.0.0.63 192.168.1.128 0.0.0.127
Router-A(config)# access-list 101 deny icmp 192.168.1.0 0.0.0.63 192.168.2.0 0.0.0.127
Router-A(config)# access-list 101 deny icmp 192.168.1.0 0.0.0.63 192.168.3.0 0.0.0.31
Router-A(config)# access-list 101 deny icmp 192.168.1.0 0.0.0.63 192.168.3.64 0.0.0.63
Router-A(config)# access-list 101 deny icmp 192.168.1.0 0.0.0.63 192.168.3.128 0.0.0.63
Router-A(config)# access-list 101 permit ip any any

! ACL áp cho PB Kinh doanh (A2 – 192.168.1.128/25)
Router-A(config)# access-list 102 deny icmp 192.168.1.128 0.0.0.127 192.168.1.0 0.0.0.63
Router-A(config)# access-list 102 deny icmp 192.168.1.128 0.0.0.127 192.168.2.0 0.0.0.127
Router-A(config)# access-list 102 deny icmp 192.168.1.128 0.0.0.127 192.168.3.0 0.0.0.31
Router-A(config)# access-list 102 deny icmp 192.168.1.128 0.0.0.127 192.168.3.64 0.0.0.63
Router-A(config)# access-list 102 deny icmp 192.168.1.128 0.0.0.127 192.168.3.128 0.0.0.63
Router-A(config)# access-list 102 permit ip any any

! ACL áp cho PB Kho vận (A3 – 192.168.2.0/25)
Router-A(config)# access-list 103 deny icmp 192.168.2.0 0.0.0.127 192.168.1.0 0.0.0.63
Router-A(config)# access-list 103 deny icmp 192.168.2.0 0.0.0.127 192.168.1.128 0.0.0.127
Router-A(config)# access-list 103 deny icmp 192.168.2.0 0.0.0.127 192.168.3.0 0.0.0.31
Router-A(config)# access-list 103 deny icmp 192.168.2.0 0.0.0.127 192.168.3.64 0.0.0.63
Router-A(config)# access-list 103 deny icmp 192.168.2.0 0.0.0.127 192.168.3.128 0.0.0.63
Router-A(config)# access-list 103 permit ip any any

! Áp ACL vào đúng interface — chiều "in" (gói tin từ phòng ban đi vào Router)
Router-A(config)# interface GigabitEthernet0/0
Router-A(config-if)# ip access-group 101 in
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/1
Router-A(config-if)# ip access-group 102 in
Router-A(config-if)# exit

Router-A(config)# interface GigabitEthernet0/2
Router-A(config-if)# ip access-group 103 in
Router-A(config-if)# exit

Router-A(config)# end
Router-A# write memory
```

> **Ghi chú:** Interface Gi0/3 (Server Farm A) **không** áp ACL — mọi phòng ban vẫn cần truy cập DHCP/DNS/HTTP/FTP/Email từ Server-A, và việc ping đến Server để kiểm tra kết nối vẫn được cho phép.

## 3.4.3 ACL trên Router-B

```bash
Router-B# configure terminal

! ACL áp cho PB IT/Điều hành (B1 – 192.168.3.0/27)
Router-B(config)# access-list 111 deny icmp 192.168.3.0 0.0.0.31 192.168.3.64 0.0.0.63
Router-B(config)# access-list 111 deny icmp 192.168.3.0 0.0.0.31 192.168.3.128 0.0.0.63
Router-B(config)# access-list 111 deny icmp 192.168.3.0 0.0.0.31 192.168.1.0 0.0.0.63
Router-B(config)# access-list 111 deny icmp 192.168.3.0 0.0.0.31 192.168.1.128 0.0.0.127
Router-B(config)# access-list 111 deny icmp 192.168.3.0 0.0.0.31 192.168.2.0 0.0.0.127
Router-B(config)# access-list 111 permit ip any any

! ACL áp cho PB Kho vận (B2 – 192.168.3.64/26)
Router-B(config)# access-list 112 deny icmp 192.168.3.64 0.0.0.63 192.168.3.0 0.0.0.31
Router-B(config)# access-list 112 deny icmp 192.168.3.64 0.0.0.63 192.168.3.128 0.0.0.63
Router-B(config)# access-list 112 deny icmp 192.168.3.64 0.0.0.63 192.168.1.0 0.0.0.63
Router-B(config)# access-list 112 deny icmp 192.168.3.64 0.0.0.63 192.168.1.128 0.0.0.127
Router-B(config)# access-list 112 deny icmp 192.168.3.64 0.0.0.63 192.168.2.0 0.0.0.127
Router-B(config)# access-list 112 permit ip any any

! ACL áp cho PB Kỹ thuật (B3 – 192.168.3.128/26)
Router-B(config)# access-list 113 deny icmp 192.168.3.128 0.0.0.63 192.168.3.0 0.0.0.31
Router-B(config)# access-list 113 deny icmp 192.168.3.128 0.0.0.63 192.168.3.64 0.0.0.63
Router-B(config)# access-list 113 deny icmp 192.168.3.128 0.0.0.63 192.168.1.0 0.0.0.63
Router-B(config)# access-list 113 deny icmp 192.168.3.128 0.0.0.63 192.168.1.128 0.0.0.127
Router-B(config)# access-list 113 deny icmp 192.168.3.128 0.0.0.63 192.168.2.0 0.0.0.127
Router-B(config)# access-list 113 permit ip any any

! Áp ACL vào interface
Router-B(config)# interface GigabitEthernet0/0
Router-B(config-if)# ip access-group 111 in
Router-B(config-if)# exit

Router-B(config)# interface GigabitEthernet0/1
Router-B(config-if)# ip access-group 112 in
Router-B(config-if)# exit

Router-B(config)# interface GigabitEthernet0/2
Router-B(config-if)# ip access-group 113 in
Router-B(config-if)# exit

Router-B(config)# end
Router-B# write memory
```

## 3.4.4 Kiểm tra ACL

```bash
! Xem nội dung ACL
Router-A# show access-lists
Router-A# show ip interface GigabitEthernet0/0   ! kiểm tra ACL đã gắn đúng chiều "in"

! Test từ PC phòng IT (A1) – PHẢI thất bại (Request timed out)
C:\> ping 192.168.1.130     ! PC phòng Kinh doanh (A2)
C:\> ping 192.168.3.2       ! PC phòng IT CN B (B1)

! Test từ PC phòng IT (A1) – PHẢI thành công (cùng phòng ban)
C:\> ping 192.168.1.10      ! PC khác trong cùng PB IT

! Test các dịch vụ khác vẫn hoạt động bình thường dù ping bị chặn
C:\> ftp 192.168.1.66
C:\> nslookup logistics.local    ! DNS vẫn phân giải (UDP, không bị chặn)
```

---



## 4.1 Server-A (CORE) – 1 SERVER DUY NHẤT cho Chi nhánh A (IP: 192.168.1.66/28)

> **Gộp 1 server duy nhất:** Theo yêu cầu tối ưu hóa hệ thống, toàn bộ 6 dịch vụ (DHCP, DNS, NTP, HTTP/HTTPS, Email, FTP) của Chi nhánh A được chạy trên **một server vật lý duy nhất – Server-A**, thay vì tách thành 3 server (Server-A1/A2/A3) như phương án cũ. Packet Tracer cho phép 1 server bật đồng thời nhiều dịch vụ trong tab **Services**, nên việc gộp này hoàn toàn khả thi và giúp giảm số thiết bị, số cổng switch cần dùng.

### Cấu hình địa chỉ IP tĩnh (trong Packet Tracer)

| Thông số | Giá trị |
|----------|---------|
| IP Address | 192.168.1.66 |
| Subnet Mask | 255.255.255.240 |
| Default Gateway | 192.168.1.65 |
| DNS Server | 192.168.1.66 (tự phân giải) |

---

### 4.1.1 Dịch vụ DHCP (Server-A)

> Vào **Desktop → IP Configuration**: đặt IP tĩnh như trên.
> Vào **Services → DHCP**: Bật **ON**

**Tạo Pool cho từng Subnet (nếu dùng Server làm DHCP thay Router):**

| Pool Name | Default Gateway | DNS Server | Start IP | Max Users | Subnet Mask |
|-----------|----------------|------------|----------|-----------|-------------|
| POOL-A1-IT | 192.168.1.1 | 192.168.1.66 | 192.168.1.11 | 52 | 255.255.255.192 |
| POOL-A2-KD | 192.168.1.129 | 192.168.1.66 | 192.168.1.136 | 118 | 255.255.255.128 |
| POOL-A3-KV | 192.168.2.1 | 192.168.1.66 | 192.168.2.6 | 121 | 255.255.255.128 |
| POOL-ASRV | 192.168.1.65 | 192.168.1.66 | 192.168.1.69 | 10 | 255.255.255.240 |

> **Lưu ý:** Chỉ cần Exclude IP `192.168.1.66` (địa chỉ của Server-A) và các Gateway — vì giờ chỉ còn 1 server duy nhất, không cần loại trừ thêm `.67`/`.68` như trước.

---

### 4.1.2 Dịch vụ DNS (Server-A)

> Vào **Services → DNS**: Bật **ON**

**Bảng DNS Records:** (tất cả trỏ về cùng 1 IP `192.168.1.66` vì chỉ còn 1 server)

| Tên miền nội bộ | Loại Record | Địa chỉ IP | Mô tả |
|-----------------|-------------|------------|-------|
| `logistics.local` | A Record | 192.168.1.66 | Web Server chính |
| `www.logistics.local` | A Record | 192.168.1.66 | Web Server (alias) |
| `ftp.logistics.local` | A Record | 192.168.1.66 | FTP Server |
| `mail.logistics.local` | A Record | 192.168.1.66 | Email Server |
| `ntp.logistics.local` | A Record | 192.168.1.66 | NTP Server |
| `server-a.logistics.local` | A Record | 192.168.1.66 | Server CORE (DHCP/DNS/NTP/HTTP/Email/FTP) |
| `router-a.logistics.local` | A Record | 192.168.1.1 | Router CN A |
| `router-b.logistics.local` | A Record | 192.168.3.1 | Router CN B |

**Các bước thực hiện trong Packet Tracer:**
1. Mở Server-A → tab **Services** → chọn **DNS**
2. Bật **DNS Service: ON**
3. Nhập từng record vào ô **Name** và **Address**, chọn loại **A Record**
4. Nhấn **Add** sau mỗi record

---

### 4.1.3 Dịch vụ NTP (Server-A)

> Vào **Services → NTP**: Bật **ON**

**Cấu hình NTP trong Packet Tracer:**
1. Mở Server-A → tab **Services** → chọn **NTP**
2. Bật **NTP Service: ON**
3. Đặt thời gian chính xác (ngày/giờ hiện tại)

**Lệnh trên Router-A (NTP Master):**

```bash
! Router-A phát NTP cho toàn mạng (stratum 3)
Router-A(config)# ntp master 3
Router-A(config)# ntp update-calendar

! Cấu hình múi giờ (UTC+7 – Việt Nam)
Router-A(config)# clock timezone ICT 7
```

**Lệnh trên Router-B (NTP Client):**

```bash
! Router-B đồng bộ từ Router-A
Router-B(config)# ntp server 10.0.0.1
Router-B(config)# clock timezone ICT 7
```

**Kiểm tra NTP:**

```bash
Router-A# show ntp status
Router-A# show clock
Router-B# show ntp associations
```

---

### 4.1.4 Dịch vụ HTTP/HTTPS (Server-A)

> Vào **Services → HTTP**: Bật **HTTP ON** và **HTTPS ON**

**Cấu hình trong Packet Tracer:**
1. Mở Server-A → tab **Services** → chọn **HTTP**
2. Bật **HTTP Service: ON**
3. Bật **HTTPS Service: ON**
4. Chỉnh sửa file `index.html` mặc định với nội dung tùy chỉnh:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Trung tâm Logistics & Kho bãi thông minh</title>
</head>
<body>
  <h1>Chào mừng đến Hệ thống Nội bộ Logistics</h1>
  <p>Portal quản lý kho bãi và vận chuyển nội bộ.</p>
  <ul>
    <li>Quản lý đơn hàng</li>
    <li>Theo dõi hàng hóa</li>
    <li>Báo cáo vận hành</li>
  </ul>
</body>
</html>
```

**Kiểm tra từ PC bất kỳ:**
- Mở Web Browser → nhập `http://logistics.local` hoặc `http://192.168.1.66`
- Kết quả mong đợi: Trang web nội bộ hiển thị

---

### 4.1.5 Dịch vụ Email – SMTP + POP3 (Server-A)

> Vào **Services → EMAIL**: Bật **SMTP ON** và **POP3 ON**

**Cấu hình Email Server:**
1. Mở Server-A → tab **Services** → chọn **EMAIL**
2. Bật **SMTP Service: ON**
3. Bật **POP3 Service: ON**
4. Nhập **Domain Name:** `logistics.local`
5. Tạo các tài khoản người dùng:

| Username | Password | Mô tả |
|----------|----------|-------|
| admin | Admin@123 | Quản trị viên hệ thống |
| it-dept | IT@123 | Phòng IT |
| sales | Sales@123 | Phòng Kinh doanh |
| warehouse | WH@123 | Phòng Kho vận |

**Cấu hình Email Client trên PC (Packet Tracer):**
- Mở PC → tab **Desktop** → chọn **Email**
- **Your Name:** Tên người dùng
- **Email Address:** `username@logistics.local`
- **Incoming Mail Server (POP3):** `192.168.1.66` hoặc `mail.logistics.local`
- **Outgoing Mail Server (SMTP):** `192.168.1.66` hoặc `mail.logistics.local`
- **User Name:** tên đăng nhập
- **Password:** mật khẩu tương ứng

---

### 4.1.6 Dịch vụ FTP (Server-A)

> Vào **Services → FTP**: Bật **ON**

**Cấu hình FTP Server trong Packet Tracer:**
1. Mở Server-A → tab **Services** → chọn **FTP**
2. Bật **FTP Service: ON**
3. Tạo tài khoản FTP:

| Username | Password | Quyền truy cập |
|----------|----------|----------------|
| ftp_logistics | FTP@Logistics | Read, Write, Delete, Rename, List |
| ftp_readonly | ReadOnly@123 | Read, List |
| ftp_warehouse | WH_FTP@123 | Read, Write, List |

4. Cấu hình quyền: Tích chọn **Read ✓**, **Write ✓**, **Delete ✓**, **Rename ✓**, **List ✓**

**Truy cập FTP từ PC (Command Prompt):**

```bash
! Mở PC → Desktop → Command Prompt
ftp 192.168.1.66
! hoặc
ftp ftp.logistics.local

! Đăng nhập
Username: ftp_logistics
Password: FTP@Logistics

! Các lệnh FTP cơ bản
ftp> dir          ! Liệt kê file
ftp> get file.txt ! Tải file về
ftp> put file.txt ! Upload file lên
ftp> quit         ! Thoát
```

---


## 4.2 Server-B – 1 SERVER DUY NHẤT cho Chi nhánh B – DHCP (IP: 192.168.3.194/29)

### Cấu hình địa chỉ IP tĩnh

| Thông số | Giá trị |
|----------|---------|
| IP Address | 192.168.3.194 |
| Subnet Mask | 255.255.255.248 |
| Default Gateway | 192.168.3.193 |
| DNS Server | 192.168.1.66 |

---

### 4.2.1 Dịch vụ DHCP – Server-B (Dành cho Chi nhánh B)

> Vào **Services → DHCP**: Bật **ON**

**Tạo DHCP Pool cho Chi nhánh B:**

| Pool Name | Default Gateway | DNS Server | Start IP | Max Users | Subnet Mask |
|-----------|----------------|------------|----------|-----------|-------------|
| POOL-B1-IT | 192.168.3.1 | 192.168.1.66 | 192.168.3.2 | 29 | 255.255.255.224 |
| POOL-B2-KV | 192.168.3.65 | 192.168.1.66 | 192.168.3.66 | 62 | 255.255.255.192 |
| POOL-B3-KT | 192.168.3.129 | 192.168.1.66 | 192.168.3.130 | 62 | 255.255.255.192 |

**Lệnh DHCP Pool trên Router-B (phương án dự phòng):**

```bash
! Loại trừ IP Gateway và Server
Router-B(config)# ip dhcp excluded-address 192.168.3.1 192.168.3.5
Router-B(config)# ip dhcp excluded-address 192.168.3.65 192.168.3.70
Router-B(config)# ip dhcp excluded-address 192.168.3.129 192.168.3.133
Router-B(config)# ip dhcp excluded-address 192.168.3.193 192.168.3.198

! DHCP Pool cho Phòng Điều hành & IT (CN B) – Subnet B1
Router-B(config)# ip dhcp pool POOL-B1-IT
Router-B(dhcp-config)# network 192.168.3.0 255.255.255.224
Router-B(dhcp-config)# default-router 192.168.3.1
Router-B(dhcp-config)# dns-server 192.168.1.66
Router-B(dhcp-config)# domain-name logistics.local
Router-B(dhcp-config)# lease 1
Router-B(dhcp-config)# exit

! DHCP Pool cho Phòng Kho vận (CN B) – Subnet B2
Router-B(config)# ip dhcp pool POOL-B2-KV
Router-B(dhcp-config)# network 192.168.3.64 255.255.255.192
Router-B(dhcp-config)# default-router 192.168.3.65
Router-B(dhcp-config)# dns-server 192.168.1.66
Router-B(dhcp-config)# domain-name logistics.local
Router-B(dhcp-config)# lease 1
Router-B(dhcp-config)# exit

! DHCP Pool cho Phòng Kỹ thuật (CN B) – Subnet B3
Router-B(config)# ip dhcp pool POOL-B3-KT
Router-B(dhcp-config)# network 192.168.3.128 255.255.255.192
Router-B(dhcp-config)# default-router 192.168.3.129
Router-B(dhcp-config)# dns-server 192.168.1.66
Router-B(dhcp-config)# domain-name logistics.local
Router-B(dhcp-config)# lease 1
Router-B(dhcp-config)# exit

Router-B(config)# end
Router-B# write memory
```

---

## 4.3 Tóm tắt Dịch vụ trên từng Thiết bị

| Thiết bị | IP | DHCP | DNS | HTTP/HTTPS | FTP | Email | SSH | NTP |
|----------|-----|------|-----|-----------|-----|-------|-----|-----|
| Router-A | 10.0.0.1 / 192.168.x.x | ✅ Server | – | – | – | – | ✅ | ✅ Master |
| Router-B | 10.0.0.2 / 192.168.x.x | ✅ Server | – | – | – | – | ✅ | ✅ Client |
| Server-A | 192.168.1.66 | ✅ | ✅ | ✅ | ✅ | ✅ SMTP/POP3 | – | ✅ |
| Server-B | 192.168.3.194 | ✅ (CN B) | – | – | – | – | – | – |

---

## 4.4 Kiểm tra và Xác nhận Dịch vụ

### Lệnh kiểm tra toàn hệ thống

```bash
! ===== Kiểm tra DHCP =====
! Từ PC: Desktop → IP Configuration → chọn DHCP
! Kiểm tra trên Router
Router-A# show ip dhcp binding
Router-A# show ip dhcp pool

! ===== Kiểm tra DNS =====
! Từ PC: Desktop → Command Prompt
ping logistics.local
ping www.logistics.local
nslookup logistics.local

! ===== Kiểm tra HTTP =====
! Từ PC: Desktop → Web Browser
! Nhập: http://logistics.local

! ===== Kiểm tra FTP =====
! Từ PC: Desktop → Command Prompt
ftp ftp.logistics.local
! Hoặc: ftp 192.168.1.66

! ===== Kiểm tra Email =====
! Từ PC: Desktop → Email → gửi thử tới admin@logistics.local

! ===== Kiểm tra SSH =====
! Từ PC: Desktop → Command Prompt
ssh -l admin 192.168.1.1
! Hoặc từ Router khác:
Router-B# ssh -l admin 192.168.1.1

! ===== Kiểm tra NTP =====
Router-A# show clock
Router-A# show ntp status
Router-B# show ntp associations
Router-B# show clock

! ===== Kiểm tra kết nối liên chi nhánh =====
Router-A# ping 10.0.0.2
Router-A# ping 192.168.3.1
Router-A# ping 192.168.3.65
Router-A# ping 192.168.3.194

! ===== Kiểm tra routing =====
Router-A# show ip route
Router-B# show ip route
```

---

# PHỤ LỤC – BẢNG VLSM TỔNG HỢP

## Chi nhánh A – Trụ sở chính

| Subnet | Phòng ban | Host cần | Mask | Network | Gateway | Usable Range | Broadcast |
|--------|-----------|----------|------|---------|---------|--------------|-----------|
| A1 | IT & Điều hành | 30 | /26 | 192.168.1.0 | 192.168.1.1 | .2 – .62 | 192.168.1.63 |
| A-SRV | Server Farm | 10 | /28 | 192.168.1.64 | 192.168.1.65 | .66 – .78 | 192.168.1.79 |
| A2 | Kinh doanh | 80 | /25 | 192.168.1.128 | 192.168.1.129 | .130 – .254 | 192.168.1.255 |
| A3 | Kho vận | 70 | /25 | 192.168.2.0 | 192.168.2.1 | .2 – .126 | 192.168.2.127 |

## Chi nhánh B – Kho vệ tinh

| Subnet | Phòng ban | Host cần | Mask | Network | Gateway | Usable Range | Broadcast |
|--------|-----------|----------|------|---------|---------|--------------|-----------|
| B1 | IT & Điều hành | 20 | /27 | 192.168.3.0 | 192.168.3.1 | .2 – .30 | 192.168.3.31 |
| B2 | Kho vận | 60 | /26 | 192.168.3.64 | 192.168.3.65 | .66 – .126 | 192.168.3.127 |
| B3 | Kỹ thuật | 40 | /26 | 192.168.3.128 | 192.168.3.129 | .130 – .190 | 192.168.3.191 |
| B-SRV | Server Farm | 5 | /29 | 192.168.3.192 | 192.168.3.193 | .194 – .198 | 192.168.3.199 |

## Kết nối WAN

| Liên kết | Mask | Network | Router-A | Router-B | Broadcast |
|----------|------|---------|---------|---------|-----------|
| WAN A–B | /30 | 10.0.0.0 | 10.0.0.1 (DCE) | 10.0.0.2 (DTE) | 10.0.0.3 |

---

*Tài liệu được tổng hợp và hoàn thiện cho môn NWC204 – SU26*
*Mô hình: Trung tâm Logistics & Kho bãi thông minh*
*Phần mềm mô phỏng: Cisco Packet Tracer*