# 📘 TÀI LIỆU TỔNG THỂ DỰ ÁN HIE SYSTEM
## HỆ THỐNG TRAO ĐỔI THÔNG TIN Y TẾ ĐIỆN TỬ

> **Tài liệu dành cho:** Hiring Managers, Business Analysts, Product Owners, và người không chuyên kỹ thuật  
> **Version:** 1.0  
> **Ngày cập nhật:** 11/07/2026

---

## 📋 MỤC LỤC

1. [TỔNG QUAN DỰ ÁN](#phần-1-tổng-quan-dự-án)
2. [KIẾN TRÚC HỆ THỐNG](#phần-2-kiến-trúc-hệ-thống)
3. [CÁC NỀN TẢNG ỨNG DỤNG](#phần-3-các-nền-tảng-ứng-dụng)
4. [BACKEND MICROSERVICES](#phần-4-backend-microservices)
5. [CÔNG NGHỆ SỬ DỤNG](#phần-5-công-nghệ-sử-dụng)
6. [QUY TRÌNH NGHIỆP VỤ](#phần-6-quy-trình-nghiệp-vụ)
7. [BẢO MẬT & QUYỀN TRUY CẬP](#phần-7-bảo-mật-và-quyền-truy-cập)
8. [TRIỂN KHAI & VẬN HÀNH](#phần-8-triển-khai-và-vận-hành)
9. [TÍNH NĂNG NỔI BẬT](#phần-9-tính-năng-nổi-bật)
10. [ROADMAP](#phần-10-roadmap-tương-lai)

---

## 📋 PHẦN 1: TỔNG QUAN DỰ ÁN

### 1.1. Giới thiệu

HIE System (Health Information Exchange) là một **hệ thống y tế điện tử toàn diện**, bao gồm:

```
HIE SYSTEM
├── Backend (20+ Microservices)
├── Web Admin (Angular)
├── Mobile App (React Native)
├── Kiosk (Self-service Terminal)
├── HIS Integration
└── E-Signature Service
```

### 1.2. Giá trị Kinh doanh


**ROI (Return on Investment) cho Bệnh viện:**
- ⬇️ Giảm 50-70% chi phí vận hành giấy tờ
- ⬆️ Tăng 30-40% hiệu suất làm việc của nhân viên
- ⬆️ Tăng 25% lượng bệnh nhân đặt lịch online
- ⬇️ Giảm 60% thời gian chờ đợi của bệnh nhân
- ✅ Đáp ứng yêu cầu chuyển đổi số của Bộ Y tế

**Lợi ích cho Bệnh nhân:**
- 📱 Đặt lịch khám mọi lúc, mọi nơi qua app
- 🏥 Check-in tự động tại Kiosk (không xếp hàng)
- 📄 Xem kết quả xét nghiệm ngay trên điện thoại
- 💳 Thanh toán online tiện lợi
- 🔔 Nhận thông báo nhắc lịch hẹn tự động
- 📚 Lưu trữ hồ sơ y tế vĩnh viễn

### 1.3. Quy mô Hệ thống

- **20+ Microservices** hoạt động độc lập
- **4 nền tảng** ứng dụng (Web, Mobile, Kiosk, HIS)
- Phục vụ **nhiều bệnh viện** (multi-tenant)
- Xử lý **hàng triệu bản ghi** bệnh nhân
- Hoạt động **24/7** với độ sẵn sàng > 99.5%
- Hỗ trợ **iOS, Android, Web browsers**

---

## 🏗️ PHẦN 2: KIẾN TRÚC HỆ THỐNG

### 2.1. Kiến trúc Tổng quan

```
┌─────────────────────────────────────────────────────────────┐
│                    NGƯỜI DÙNG (Users)                        │
├──────────────┬──────────────┬──────────────┬────────────────┤
│  Mobile App  │  Web Admin   │    Kiosk     │  HIS System    │
│ (iOS/Android)│   (Angular)  │  (Terminal)  │ (SQL Server)   │
└──────┬───────┴──────┬───────┴──────┬───────┴────────┬───────┘
       │              │              │                 │
       └──────────────┴──────────────┴─────────────────┘
                           │
                ┌──────────▼──────────┐
                │   API GATEWAY       │
                │ (AdminToolService)  │
                └──────────┬──────────┘
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
┌──────▼──────┐    ┌──────▼──────┐    ┌──────▼──────┐
│ Microservice │    │ Microservice │    │ Microservice │
│   Group 1    │    │   Group 2    │    │   Group 3    │
│  (User, Auth)│    │ (Booking,   │    │ (Kiosk,     │
│              │    │  Carebook)   │    │  Partner)    │
└──────┬───────┘    └──────┬───────┘    └──────┬───────┘
       │                   │                    │
       └───────────────────┼────────────────────┘
                           │
       ┌───────────────────┴────────────────────┐
       │                                        │
┌──────▼──────┐    ┌──────────┐    ┌──────────┐
│   MongoDB   │    │  Kafka   │    │  Redis   │
│  (Primary)  │    │ (Events) │    │ (Cache)  │
└─────────────┘    └────┬─────┘    └──────────┘
                        │
                ┌───────▼────────┐
                │  SQL Server    │
                │  (HIS Legacy)  │
                └────────────────┘
```

### 2.2. Mô hình Microservices

**Lợi ích:**
- ✅ **Độc lập:** Mỗi service phát triển và triển khai riêng
- ✅ **Mở rộng linh hoạt:** Scale từng service theo nhu cầu
- ✅ **Dễ bảo trì:** Sửa 1 service không ảnh hưởng toàn hệ thống
- ✅ **Công nghệ đa dạng:** Mỗi service dùng công nghệ phù hợp
- ✅ **DevOps-ready:** CI/CD tự động, deploy không downtime

**Ví dụ thực tế:**
Khi có 10,000 người đặt lịch cùng lúc, chỉ cần scale BookingManagement service  
mà không cần scale toàn bộ hệ thống → Tiết kiệm chi phí.

---

## 📱 PHẦN 3: CÁC NỀN TẢNG ỨNG DỤNG

### 3.1. Web Admin (09.FrontEnd) - Angular

**Người dùng:** Quản trị viên, Bác sĩ, Y tá

**Công nghệ:**
- Angular 9.1.9
- Bootstrap 4.5.0
- TypeScript 3.7.5
- Keycloak (Authentication)
- Chart.js, ApexCharts (Dashboard)
- Socket.io (Realtime)
- STOMP/WebSocket (Messaging)

**Tính năng chính:**
```
Dashboard
├── Thống kê tổng quan (bệnh nhân, doanh thu)
├── Biểu đồ realtime
└── Cảnh báo quan trọng

Quản lý Người dùng
├── CRUD bác sĩ, y tá, bệnh nhân
├── Phân quyền theo vai trò
└── Theo dõi hoạt động

Quản lý Booking
├── Danh sách lịch hẹn
├── Xác nhận/Hủy lịch
└── Xem lịch theo bác sĩ/ngày

Quản lý Carebook
├── Xem hồ sơ bệnh nhân
├── Cập nhật thông tin khám
├── Kê đơn thuốc điện tử
└── Upload kết quả xét nghiệm

Quản lý Kiosk Monitor
├── Dashboard trạng thái Kiosk
├── Online/Offline realtime
├── Lịch sử hoạt động
└── Cảnh báo offline

Báo cáo & Thống kê
├── Báo cáo doanh thu
├── Báo cáo bệnh nhân
├── Export Excel/PDF
└── Dashboard tùy chỉnh
```

**Screenshots mẫu:** (Tưởng tượng giao diện)
- Dashboard: Biểu đồ tròn phân bố bệnh nhân, line chart doanh thu theo tháng
- Kiosk Monitor: Bản đồ hiển thị các Kiosk màu xanh (online), đỏ (offline)
- Danh sách booking: Table với filter theo ngày, bác sĩ, trạng thái

### 3.2. Mobile App (11.Mobile_V2) - React Native

**Người dùng:** Bệnh nhân

**Công nghệ:**
- React Native 0.71+
- Redux + Redux Saga (State management)
- React Navigation (Routing)
- Firebase (Push notification)
- Socket.io-client (Realtime chat)
- React Native Camera (QR scan)
- React Native Biometrics (Fingerprint/FaceID)
- Lottie (Animations)

**Hỗ trợ:**
- ✅ iOS 12+
- ✅ Android 8+
- ✅ Deep linking
- ✅ Offline mode (cache data)
- ✅ Multi-language (Tiếng Việt/English)

**Tính năng chính:**

**1. Đăng ký/Đăng nhập**
- Đăng ký bằng số điện thoại + OTP
- Đăng nhập bằng username/password hoặc biometric
- Forgot password qua SMS

**2. Trang chủ (Home)**
- Banner quảng cáo dịch vụ
- Quick actions: Đặt lịch, Xem carebook, Chat bác sĩ
- Thông báo mới nhất

**3. Đặt lịch khám (Booking)**
```
Bước 1: Chọn bệnh viện
├── List các bệnh viện gần đây
├── Search theo tên
└── Xem trên bản đồ

Bước 2: Chọn chuyên khoa
├── Nội khoa, Ngoại khoa, Sản, Nhi...
└── Icon + Mô tả ngắn gọn

Bước 3: Chọn bác sĩ
├── Danh sách bác sĩ + Avatar
├── Đánh giá sao (rating)
└── Lịch trống (calendar view)

Bước 4: Điền thông tin
├── Chọn người khám (bản thân/người thân)
├── Triệu chứng (optional)
└── Ghi chú

Bước 5: Thanh toán
├── Ví điện tử (VNPay, Momo, ZaloPay)
├── Thẻ tín dụng
└── Thanh toán tại bệnh viện

Bước 6: Xác nhận
├── Nhận mã booking (BK20260712001)
├── QR code để check-in Kiosk
└── Thông báo nhắc lịch (trước 1 ngày, 1 giờ)
```

**4. Sổ khám bệnh (Carebook)**
- Timeline lịch sử khám bệnh
- Xem chi tiết: Chẩn đoán, đơn thuốc, kết quả xét nghiệm
- Download PDF kết quả
- Share qua email/Zalo

**5. Thông báo (Notifications)**
- Push notification realtime
- Inbox lưu lịch sử thông báo
- Badge count (số lượng chưa đọc)

**6. Profile**
- Thông tin cá nhân (avatar, họ tên, ngày sinh, CMND)
- Quản lý người thân
- Đổi mật khẩu
- Cài đặt ngôn ngữ
- Đăng xuất

**7. Tính năng đặc biệt**
- **Realtime updates:** Bác sĩ cập nhật → App hiển thị ngay (SSE)
- **Offline mode:** Xem carebook khi không có mạng
- **QR code:** Scan để check-in Kiosk
- **Deep linking:** Mở app từ SMS/Email notification
- **Biometric login:** Vân tay/FaceID

### 3.3. Kiosk Application (13.HIE KIOSK FE, 15.Kiosk_V2)

**Mục đích:** Máy tự phục vụ tại bệnh viện (giống ATM)

**Phần cứng:**
- Màn hình cảm ứng 22-27 inch
- Máy in nhiệt (thermal printer)
- Đầu đọc thẻ CMND/CCCD (optional)
- QR code scanner
- Camera (optional)

**Công nghệ:**
- Electron (Desktop app)
- React/Angular frontend
- Windows/Linux OS
- Printer SDK integration

**Quy trình sử dụng:**

```
┌──────────────────────────────────────┐
│  CHÀO MỪNG ĐẾN BỆNH VIỆN HOÀN MỸ    │
│                                      │
│         [Bắt đầu]                    │
│                                      │
│  Chọn ngôn ngữ: [Tiếng Việt] [EN]  │
└──────────────────────────────────────┘
              │
              ▼
┌──────────────────────────────────────┐
│  CHỌN CÁCH CHECK-IN                  │
│                                      │
│  1. [Quét QR code từ app]           │
│  2. [Nhập mã booking]               │
│  3. [Quét CMND/CCCD]                │
└──────────────────────────────────────┘
              │
              ▼
┌──────────────────────────────────────┐
│  XÁC NHẬN THÔNG TIN                  │
│                                      │
│  Họ tên: Nguyễn Văn A               │
│  Bác sĩ: BS. Trần Văn B             │
│  Khoa: Nội Tim mạch                 │
│  Giờ khám: 09:00 - 11/07/2026       │
│  Phòng: P101 - Tầng 2               │
│                                      │
│  [Xác nhận] [Hủy]                   │
└──────────────────────────────────────┘
              │
              ▼
┌──────────────────────────────────────┐
│  ✅ CHECK-IN THÀNH CÔNG               │
│                                      │
│  [Đang in phiếu khám...]            │
│                                      │
│  🗺️  Bản đồ chỉ đường:               │
│     Từ đây → Thang máy → Tầng 2     │
│     → Rẽ trái → Phòng P101          │
└──────────────────────────────────────┘
              │
              ▼
      [In phiếu hoàn tất]
```

**Kiosk Monitor (Quản lý từ xa):**
- Dashboard hiển thị tất cả Kiosk
- Trạng thái realtime: Online (xanh), Offline (đỏ), Never Connected (xám)
- Heartbeat mechanism: Kiosk gửi tín hiệu mỗi 30 giây
- Cảnh báo: Email/SMS khi Kiosk offline > 5 phút
- Lịch sử: Log mọi hoạt động check-in, lỗi máy in, v.v.

### 3.4. HIS Integration Service (10.HISService)

**Mục đích:** Kết nối HIE System với HIS (Hospital Information System) của bệnh viện

**Vấn đề:**
Mỗi bệnh viện có HIS riêng (khác vendor: Meditec, iBHYT, SoftDreams...)  
→ Cần service trung gian để chuẩn hóa dữ liệu

**Giải pháp:**
- **Adapter pattern:** Mỗi HIS vendor có 1 adapter
- **Data transformation:** Convert format HIS → HIE format
- **Sync 2-way:** HIE ↔ HIS

**Ví dụ flow:**
```
1. Bệnh nhân đặt lịch trên Mobile App
   → HIE System lưu booking vào MongoDB
   → HISService đẩy booking vào SQL Server HIS

2. Bác sĩ nhập kết quả khám trong HIS
   → HIS lưu vào SQL Server
   → Debezium CDC phát hiện thay đổi
   → Kafka consumer sync vào MongoDB
   → Mobile App nhận thông báo realtime
```

### 3.5. E-Signature Service (12.EhosSign)

**Mục đích:** Chữ ký điện tử cho tài liệu y tế

**Tính năng:**
- Ký đơn thuốc điện tử (bác sĩ)
- Ký phiếu đồng ý điều trị (bệnh nhân)
- Ký hợp đồng bảo hiểm
- Xác thực chữ ký (PKI - Public Key Infrastructure)
- Lưu trữ chứng thư số

**Quy trình:**
1. Bác sĩ kê đơn thuốc trên Web Admin
2. Chọn "Ký điện tử"
3. Nhập mã PIN hoặc scan vân tay (USB token)
4. Đơn thuốc được ký, không thể chỉnh sửa
5. Hash + timestamp được lưu vào blockchain (optional)

---

