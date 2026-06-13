# PROJECT TIMELINE & OBJECTIVES

## NWC204 – SU26

# Tổng quan dự án

Dự án được chia thành **2 giai đoạn chính trong 7 tuần**, tập trung vào việc thiết kế, triển khai và bảo mật hệ thống mạng doanh nghiệp bằng **Cisco Packet Tracer**.

Mục tiêu cuối cùng là xây dựng một hệ thống mạng hoàn chỉnh, hoạt động ổn định, có kiểm soát truy cập và đầy đủ dịch vụ mạng.

---

# GIAI ĐOẠN 1 – THIẾT KẾ HẠ TẦNG, LẬP KẾ HOẠCH IP & ĐỊNH TUYẾN

## (Tuần 1 → Tuần 4)

Mục tiêu của giai đoạn này là xây dựng nền tảng kiến trúc mạng và đảm bảo khả năng định tuyến trước khi áp dụng các chính sách bảo mật.

---

## Tuần 1 – Thành lập nhóm & lựa chọn mô hình doanh nghiệp

### Mục tiêu

* Hoàn thành đăng ký nhóm.
* Lựa chọn lĩnh vực mô phỏng doanh nghiệp.

Ví dụ:

* Trung tâm Logistics
* Studio Hoạt hình số
* Bệnh viện công nghệ cao

### Công việc cần thực hiện

* Phân chia vai trò trong nhóm.
* Viết **Phần 1 của báo cáo**:

  * Tóm tắt dự án (Executive Summary)
  * Giới thiệu doanh nghiệp
  * Phân tích yêu cầu người dùng và thiết bị

---

## Tuần 2 – Thiết kế Topology & chia mạng bằng VLSM

### Mục tiêu

* Hoàn thiện sơ đồ mạng vật lý.
* Tối ưu không gian địa chỉ IPv4.

### Yêu cầu kỹ thuật

⚠️ Không được sử dụng:

* VLAN
* Layer 3 Switch

### Công việc cần thực hiện

* Xây dựng mô hình trên Cisco Packet Tracer.
* Mỗi phòng ban phải kết nối trực tiếp qua:

  * Router Interface riêng
  * hoặc Switch Layer 2 riêng biệt.

### Kết nối WAN giữa các chi nhánh

* Serial
* Ethernet trực tiếp

### Báo cáo cần có

* Bảng VLSM:

  * Network Address
  * Gateway
  * Usable IP Range
  * Subnet Mask

---

## Tuần 3 – Cấu hình nền tảng & định tuyến liên chi nhánh

### Mục tiêu

Thiết lập khả năng kết nối toàn bộ hệ thống.

### Công việc cần thực hiện

#### Cấu hình bảo mật cơ bản

```bash
hostname
banner motd
enable secret
```

#### Cấu hình định tuyến

```bash
Static Routing
```

### Điều kiện bắt buộc

✅ Tất cả thiết bị phải ping được nhau.

---

## Tuần 4 – Dịch vụ mạng & Đánh giá giữa kỳ

### Mục tiêu

* Tự động cấp phát IP.
* Phân giải tên miền nội bộ.

### Công việc cần thực hiện

#### DHCP

* Dùng Server hoặc Router làm DHCP Server.

Vì mỗi subnet đi qua Router riêng nên cần:

```bash
ip helper-address
```

#### DNS

Ví dụ:

```text
company.local
```

Ánh xạ tới:

```text
HTTP Server nội bộ
```

---

# MIDTERM CHECKPOINT (Cuối tuần 4)

## Hình thức

Demo trực tiếp file Packet Tracer.

## Nội dung kiểm tra

### 1. Kiểm tra Topology

* Không VLAN
* Không Layer 3 Switch

### 2. Kiểm tra VLSM

* Đúng tính toán
* Đúng tài liệu

### 3. Kiểm tra thực tế

Giảng viên sẽ:

* Chọn ngẫu nhiên máy tính.
* Kiểm tra DHCP.
* Kiểm tra Ping tới Server.

⚠️ Phải đạt mới được vào Giai đoạn 2.

---

# GIAI ĐOẠN 2 – ACL, DỊCH VỤ NÂNG CAO & BÀN GIAO

## (Tuần 5 → Tuần 7)

Mục tiêu:

* Cô lập mạng.
* Triển khai dịch vụ ứng dụng.
* Hoàn thiện hồ sơ dự án.

---

## Tuần 5 – ACL & Dịch vụ mở rộng

### Mục tiêu

Ngăn các phòng ban truy cập lẫn nhau nhưng vẫn truy cập Server.

### Công việc

#### ACL mở rộng

Logic:

```text
Permit → Server Subnet
↓
Deny → Department Subnet
↓
Permit → Other Traffic
```

Áp dụng:

```bash
ip access-group <num> in
```

### Dịch vụ bổ sung

#### Email Server

* Domain nội bộ
* Mailbox người dùng

#### FTP Server

* Tạo thư mục
* Cấp quyền tài khoản

---

## Tuần 6 – Bảo mật hạ tầng & Hoàn thiện báo cáo

### Mục tiêu

Bảo vệ truy cập quản trị.

### Công việc

#### Tắt Telnet

#### Cấu hình SSH

```bash
transport input ssh
```

#### AAA + RADIUS

Xác thực tập trung:

```text
Router/Switch
↓
RADIUS Server
↓
Database xác thực
```

### Báo cáo

* Chụp cấu hình quan trọng
* Chứng minh ACL hoạt động

---

## Tuần 7 – Tối ưu & Bảo vệ cuối kỳ

### Sản phẩm cần nộp

## 1. File Packet Tracer (.pkt)

Yêu cầu:

* Không lỗi
* Đầy đủ cấu hình
* ACL hoạt động

---

## 2. Báo cáo (Word/PDF)

Bao gồm:

* Giới thiệu
* Phân tích yêu cầu
* Topology
* Bảng VLSM
* Code cấu hình
* Ảnh xác thực

---

## 3. Slide trình bày

PowerPoint hoặc Canva:

* Tóm tắt kiến trúc
* Giải pháp triển khai

---

# Hình thức bảo vệ

### Thời lượng

15 phút

### Bao gồm

* Thuyết trình
* Demo trực tiếp
* Hỏi đáp cá nhân

Ví dụ câu hỏi:

* Giải thích ACL
* Giải thích RADIUS
* Giải thích câu lệnh cấu hình

---

# Thang điểm

## Midterm – 40%

Đánh giá:

* Topology
* VLSM
* Routing
* DHCP Relay

---

## Final – 60%

Đánh giá:

* ACL hoạt động đúng
* Email
* FTP
* AAA/RADIUS
* SSH
* Báo cáo
* Khả năng trả lời cá nhân

---

# Checklist hoàn thành

* [ ] Chọn đề tài doanh nghiệp
* [ ] Thiết kế Topology
* [ ] Chia mạng VLSM
* [ ] Cấu hình Routing
* [ ] DHCP + DNS
* [ ] ACL
* [ ] Email + FTP
* [ ] SSH + AAA
* [ ] Hoàn thiện báo cáo
* [ ] Chuẩn bị bảo vệ
