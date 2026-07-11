# 🏦 HỆ THỐNG E-BANKING - TÀI LIỆU TỔNG QUAN DỰ ÁN

## 📋 THÔNG TIN DỰ ÁN

**Tên dự án:** E-Banking Microservices Platform  
**Phiên bản:** 1.0.0  
**Kiến trúc:** Microservices Architecture  
**Ngôn ngữ chính:** Java 17 (Backend), TypeScript/React (Frontend)

---

## 🎯 MÔ TẢ TỔNG QUAN

Hệ thống E-Banking là một nền tảng ngân hàng điện tử hiện đại được xây dựng theo kiến trúc microservices, cung cấp đầy đủ các tính năng của một ứng dụng banking toàn diện bao gồm xác thực, quản lý người dùng, giao dịch, trò chuyện, chatbot AI, eKYC, và thông báo đa kênh.

---

## 🏗️ KIẾN TRÚC HỆ THỐNG

### Kiến trúc Microservices

Hệ thống được thiết kế theo mô hình microservices với **10 services độc lập**, mỗi service đảm nhận một chức năng nghiệp vụ cụ thể:

```
API Gateway (Nginx)
         ↓
    ┌────────────────────────────────────┐
    │   Service Discovery (Eureka)       │
    └────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────┐
│   10 Microservices (Spring Boot)        │
│   - authService (8081)                  │
│   - userService (8082)                  │
│   - transactionService (8083)           │
│   - chatService (8084)                  │
│   - chatbotService (8085)               │
│   - ekycService (8086)                  │
│   - emailService (8087)                 │
│   - firebaseService (8088)              │
│   - socket (8089)                       │
│   - adminTool (8080)                    │
└─────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────┐
│   Infrastructure Layer                  │
│   - MySQL 8.0 (Database)                │
│   - Redis 7 (Cache & Session)           │
│   - Kafka + Zookeeper (Message Queue)   │
│   - Prometheus + Grafana (Monitoring)   │
└─────────────────────────────────────────┘
```

### Phương thức giao tiếp giữa các services

1. **Đồng bộ (Synchronous):**
   - **gRPC**: Dùng cho các thao tác quan trọng (xác thực, thông tin người dùng)
   - **HTTP REST**: Frontend gọi API Gateway

2. **Bất đồng bộ (Asynchronous):**
   - **Kafka**: Event streaming cho giao dịch, thông báo
   - **WebSocket**: Giao tiếp real-time cho chat và notification

---

## 🔧 CÔNG NGHỆ SỬ DỤNG

### Backend Stack

| Công nghệ | Phiên bản | Mục đích sử dụng |
|-----------|-----------|------------------|
| **Java** | 17 | Ngôn ngữ lập trình chính |
| **Spring Boot** | 3.2.0 - 3.5.7 | Framework microservices |
| **Spring Cloud** | 2023.0.0 | Service discovery, config |
| **Spring Security** | 3.x | Xác thực và phân quyền |
| **MySQL** | 8.0 | Database chính |
| **Redis** | 7 Alpine | Caching & Session store |
| **Apache Kafka** | 7.4.0 | Message streaming |
| **gRPC** | 1.72.0 | Inter-service communication |
| **Protocol Buffers** | 4.30.2 | Serialization cho gRPC |
| **JWT** | - | Token-based authentication |
| **Flyway** | - | Database migration |
| **Docker** | - | Containerization |

### Frontend Stack

| Công nghệ | Phiên bản | Mục đích sử dụng |
|-----------|-----------|------------------|
| **React** | 19.1.0 | UI Framework |
| **TypeScript** | 5.8.3 | Type-safe JavaScript |
| **Vite** | 7.0.4 | Build tool & dev server |
| **TailwindCSS** | 4.1.16 | CSS framework |
| **Zustand** | 5.0.8 | State management |
| **Axios** | 1.13.2 | HTTP client |
| **React Router** | 7.9.5 | Client-side routing |
| **React Hook Form** | 7.54.2 | Form validation |
| **Zod** | 3.24.1 | Schema validation |
| **Recharts** | 2.15.0 | Data visualization |
| **Framer Motion** | 11.11.17 | Animation library |
| **i18next** | 24.0.2 | Internationalization |
| **Radix UI** | - | Accessible UI components |

### Infrastructure & DevOps

- **Docker & Docker Compose**: Container orchestration
- **Jenkins**: CI/CD pipeline
- **Nginx**: API Gateway & reverse proxy
- **Eureka**: Service discovery
- **Prometheus + Grafana**: Monitoring & metrics
- **Swagger/OpenAPI**: API documentation

---

## 📦 CÁC MICROSERVICES CHI TIẾT

### 1. 🔐 authService (Port 8081)

**Chức năng chính:**
- Xác thực người dùng (Login/Logout)
- Quản lý JWT tokens (access token & refresh token)
- Tích hợp Spring Security
- gRPC server cho các service khác gọi

**Công nghệ:**
- Spring Security
- JWT
- gRPC
- MySQL (bảng users, tokens)

**APIs chính:**
- `POST /api/auth/login` - Đăng nhập
- `POST /api/auth/register` - Đăng ký
- `POST /api/auth/logout` - Đăng xuất
- `POST /api/auth/refresh` - Refresh token

### 2. 👤 userService (Port 8082)

**Chức năng chính:**
- Quản lý thông tin người dùng (CRUD)
- Quản lý profile, avatar
- Lưu trữ trạng thái eKYC
- Tích hợp Kafka consumer/producer
- Sử dụng Redis cho caching

**Công nghệ:**
- Spring Data JPA
- Redis
- Kafka
- MySQL (bảng user_profiles, user_details)

**APIs chính:**
- `GET /api/users/{id}` - Lấy thông tin user
- `PUT /api/users/{id}` - Cập nhật user
- `GET /api/users/username/{username}` - Tìm user theo username
- `POST /api/users/avatar` - Upload avatar

---

### 3. 💰 transactionService (Port 8083)

**Chức năng chính:**
- Xử lý giao dịch chuyển tiền
- Quản lý lịch sử giao dịch
- Tích hợp Kafka cho event streaming
- Sử dụng Redis cho transaction caching
- gRPC client để gọi authService

**Công nghệ:**
- Spring Data JPA
- Kafka (producer/consumer)
- Redis
- gRPC client
- MySQL (bảng transactions, accounts)

**Kafka Topics:**
- `transaction.initiated` - Giao dịch bắt đầu
- `transaction.completed` - Giao dịch hoàn thành
- `transaction.failed` - Giao dịch thất bại

**APIs chính:**
- `POST /api/transactions` - Tạo giao dịch mới
- `GET /api/transactions/user/{userId}` - Lịch sử giao dịch
- `GET /api/transactions/{transactionId}` - Chi tiết giao dịch
- `POST /api/transactions/validate` - Validate trước khi giao dịch

---

### 4. 💬 chatService (Port 8084)

**Chức năng chính:**
- Chat real-time giữa người dùng
- WebSocket cho real-time messaging
- Lưu trữ lịch sử tin nhắn
- gRPC server/client

**Công nghệ:**
- WebSocket
- Spring WebSocket
- gRPC
- MySQL (bảng messages, conversations)

**WebSocket Endpoints:**
- `/ws/chat` - WebSocket connection
- `/app/chat.sendMessage` - Gửi tin nhắn
- `/topic/messages` - Subscribe tin nhắn

**APIs chính:**
- `GET /api/chat/conversations/{userId}` - Danh sách cuộc trò chuyện
- `GET /api/chat/messages/{conversationId}` - Lịch sử tin nhắn
- `POST /api/chat/messages` - Gửi tin nhắn (fallback REST)

### 5. 🤖 chatbotService (Port 8085)

**Chức năng chính:**
- AI Chatbot sử dụng **Google Gemini API**
- Function Calling để truy vấn thông tin user
- Context-aware conversations (nhớ username trong session)
- Tích hợp với userService để lấy thông tin

**Công nghệ:**
- Google Gemini API
- Spring Web
- Function Calling AI
- MySQL (bảng chat_history)

**Function Calling hỗ trợ:**
- `getUserInfo(username)` - Lấy thông tin user
- `getUserId(username)` - Lấy userId
- `getUserDetailsWithId(username)` - Thông tin chi tiết

**APIs chính:**
- `POST /api/chatbot/ask` - Hỏi chatbot
  ```json
  {
    "username": "john_doe",
    "text": "Thông tin của tôi",
    "time": 1640995200000
  }
  ```

**Tính năng đặc biệt:**
- Tự động nhận diện context: "tôi", "của tôi" → dùng username hiện tại
- AI tự động gọi function phù hợp dựa trên câu hỏi

---

### 6. 🎫 ekycService (Port 8086)

**Chức năng chính:**
- Xác thực danh tính khách hàng (eKYC)
- Tích hợp **FPT AI SDK** cho OCR, Liveness, Face Matching
- Quản lý session eKYC
- Upload và xử lý video/image

**Công nghệ:**
- FPT AI SDK Integration
- Spring Web
- Multipart file upload
- MySQL (bảng ekyc_sessions, ekyc_results)

**Flow eKYC (SDK Integration):**
1. Frontend tạo session → `POST /api/ekyc/sessions?userId={userId}`
2. Frontend init SDK config → `POST /api/ekyc/sessions/{sessionId}/init-sdk`
3. User thực hiện eKYC trong WebView FPT AI
4. FPT AI callback kết quả:
   - OCR callback → `POST /api/ekyc/sessions/{sessionId}/ocr-callback`
   - Liveness callback → `POST /api/ekyc/sessions/{sessionId}/liveness-callback`
   - Face Match callback → `POST /api/ekyc/sessions/{sessionId}/face-match-callback`
5. Frontend check status → `GET /api/ekyc/sessions/{sessionId}`

**Session Status:**
- `INITIATED` → `OCR_COMPLETED` → `LIVENESS_COMPLETED` → `COMPLETED` / `FAILED`

**APIs chính:**
- `POST /api/ekyc/sessions` - Tạo session
- `POST /api/ekyc/sessions/{sessionId}/init-sdk` - Khởi tạo SDK config
- `POST /api/ekyc/sessions/{sessionId}/ocr-callback` - Nhận OCR data
- `POST /api/ekyc/sessions/{sessionId}/liveness-callback` - Nhận liveness
- `POST /api/ekyc/sessions/{sessionId}/face-match-callback` - Nhận face match
- `GET /api/ekyc/sessions/{sessionId}` - Check status

### 7. 📧 emailService (Port 8087)

**Chức năng chính:**
- Gửi email cho người dùng
- Retry logic khi gửi thất bại
- Template engine với Thymeleaf
- gRPC server cho các service khác

**Công nghệ:**
- Spring Mail
- Thymeleaf templates
- gRPC server
- MySQL (bảng email_logs)

**Loại email hỗ trợ:**
- Welcome email
- Verification email
- Password reset
- Transaction notification
- eKYC notification

**APIs chính:**
- `POST /api/email/send` - Gửi email đơn
- `POST /api/email/bulk` - Gửi email hàng loạt
- `GET /api/email/status/{emailId}` - Check trạng thái

---

### 8. 🔔 firebaseService (Port 8088)

**Chức năng chính:**
- Gửi push notification qua **Firebase Cloud Messaging (FCM)**
- Quản lý FCM tokens của users
- Personal notification & Bulk notification
- Lưu lịch sử notification

**Công nghệ:**
- Firebase Admin SDK
- Spring Web
- MySQL (bảng fcm_tokens, persional_noti)

**APIs chính:**
- `POST /notify/push-noti-persional` - Gửi thông báo cá nhân
  ```json
  {
    "username": "john_doe",
    "title": "Personal Message",
    "content": "This is a personal notification for you."
  }
  ```
- `POST /notify/push-noti-bulk` - Gửi thông báo hàng loạt
  ```json
  {
    "usernames": ["john_doe", "jane_smith", "bob_wilson"],
    "title": "System Maintenance",
    "content": "The system will be under maintenance..."
  }
  ```

**Tính năng đặc biệt:**
- Firebase Multicast cho bulk notification hiệu suất cao
- Detailed reporting: success/fail count, failed users list
- Database persistence cho audit trail

---

### 9. 🔌 socket (Port 8089)

**Chức năng chính:**
- WebSocket server cho real-time communication
- Kafka streaming integration
- Real-time events broadcasting

**Công nghệ:**
- Spring WebSocket
- Kafka consumer
- STOMP protocol

**Use cases:**
- Real-time transaction updates
- Live notification
- Chat message broadcasting

---

### 10. 🛠️ adminTool (Port 8080)

**Chức năng chính:**
- Admin dashboard và quản trị hệ thống
- Quản lý users, transactions
- Transaction request API
- Swagger/OpenAPI documentation
- Database migration với Flyway

**Công nghệ:**
- Spring Boot Admin
- Swagger/OpenAPI
- Flyway
- gRPC client
- MySQL admin access

**APIs chính:**
- `GET /api/admin/users` - Danh sách users
- `POST /api/admin/transactions/approve` - Duyệt giao dịch
- `GET /api/admin/statistics` - Thống kê hệ thống
- `GET /swagger-ui.html` - API documentation

**Scripts:**
- `test-transaction-api.bat` - Test script for Windows
- `test-transaction-api.sh` - Test script for Unix/Linux
- `Transaction_Request_API.postman_collection.json` - Postman collection

---

## 🎨 FRONTEND APPLICATION

### Cấu trúc Frontend (ebanking-fe)

**Framework & Build:**
- React 19.1.0 với TypeScript
- Vite 7.0.4 (build tool siêu nhanh)
- TailwindCSS 4.1.16 cho styling

**State Management:**
- Zustand 5.0.8 - lightweight state management

**Routing & Forms:**
- React Router DOM 7.9.5
- React Hook Form 7.54.2 + Zod validation

**UI Components:**
- Radix UI (accessible components)
- Lucide React (icons)
- Framer Motion (animations)

**Data Visualization:**
- Recharts 2.15.0

**Internationalization:**
- i18next 24.0.2

**HTTP Client:**
- Axios 1.13.2

**Scripts:**
```bash
npm run dev      # Development server
npm run build    # Production build
npm run preview  # Preview production build
npm run lint     # Lint code
```

---

## 🗄️ CƠ SỞ DỮ LIỆU

### MySQL Database Structure

**Database chính:** `DB_USER_SERVICE` (MySQL 8.0)

**Các bảng chính:**

1. **Authentication & Users:**
   - `users` - Thông tin user cơ bản
   - `user_profiles` - Profile chi tiết
   - `user_details` - Thông tin bổ sung
   - `tokens` - JWT tokens
   - `refresh_tokens` - Refresh tokens

2. **Transactions:**
   - `transactions` - Giao dịch
   - `accounts` - Tài khoản
   - `transaction_logs` - Audit logs

3. **Chat & Communication:**
   - `messages` - Tin nhắn
   - `conversations` - Cuộc trò chuyện
   - `chat_history` - Lịch sử chat với bot

4. **eKYC:**
   - `ekyc_sessions` - Session eKYC
   - `ekyc_results` - Kết quả xác thực

5. **Notifications:**
   - `fcm_tokens` - Firebase tokens
   - `persional_noti` - Personal notifications
   - `email_logs` - Email logs

**Migration Tool:** Flyway (version control cho database)

### Redis Cache Structure

**Use cases:**
- Session management
- User profile caching
- Transaction caching
- Rate limiting

---

## 📡 GIAO TIẾP GIỮA CÁC SERVICES

### 1. gRPC (Synchronous)

**Service-to-Service calls:**
```
authService (server) ← userService, transactionService (clients)
emailService (server) ← adminTool, transactionService (clients)
```

**Proto files location:** `src/main/proto/`

**Ví dụ gRPC service:**
- `AuthService.proto` - Authentication RPC
- `UserService.proto` - User info RPC
- `EmailService.proto` - Email sending RPC

### 2. Kafka (Asynchronous)

**Kafka Topics:**
```
transaction.initiated      → transactionService (producer)
transaction.completed      → notificationService (consumer)
transaction.failed         → emailService (consumer)
user.created               → emailService (consumer)
user.kyc.completed         → userService (consumer)
notification.send          → firebaseService (consumer)
```

**Kafka Configuration:**
- Bootstrap servers: `localhost:9092`
- Zookeeper: `localhost:2181`

### 3. WebSocket (Real-time)

**WebSocket Endpoints:**
```
ws://localhost:8084/ws/chat        - Chat service
ws://localhost:8089/ws/events      - Real-time events
```

**STOMP Destinations:**
- `/app/chat.sendMessage`
- `/topic/messages`
- `/topic/notifications`

### 4. HTTP REST (Frontend-Backend)

**API Gateway Flow:**
```
Frontend → Nginx (Port 80/443) → Backend Services (8080-8089)
```

---

## 🐳 DOCKER & DEPLOYMENT

### Docker Compose Services

**Infrastructure:**
```yaml
- zookeeper:2181
- kafka:9092
- redis:6379
- mysql:3306
- eureka-server:8761
- api-gateway:80,443
- prometheus:9090
- grafana:3000
```

**Application Services:**
```yaml
- admin-tool:8080
- auth-service:8081
- user-service:8082
- transaction-service:8083
- chat-service:8084
- chatbot-service:8085
- ekyc-service:8086
- email-service:8087
- firebase-service:8088
- socket:8089
```

### Deployment Commands

```bash
# Start all services
docker-compose up -d

# Start specific service
docker-compose up -d auth-service

# View logs
docker-compose logs -f auth-service

# Stop all services
docker-compose down

# Rebuild and restart
docker-compose up -d --build
```

---

## 🔒 BẢO MẬT

### Authentication & Authorization

**JWT Token Flow:**
1. User login → authService tạo access token (15 phút) + refresh token (7 ngày)
2. Frontend lưu tokens vào localStorage/secure storage
3. Mỗi request gửi `Authorization: Bearer {access_token}`
4. Khi access token hết hạn → gọi `/api/auth/refresh` với refresh token
5. Logout → invalidate tokens

**Spring Security:**
- CORS configuration
- CSRF protection
- Role-based access control (RBAC)
- Password encryption (BCrypt)

**API Gateway Security:**
- Rate limiting
- IP whitelisting
- SSL/TLS termination

### Data Security

- **Encryption at rest:** MySQL encrypted storage
- **Encryption in transit:** HTTPS/TLS, gRPC with TLS
- **Sensitive data:** .env files (không commit lên git)
- **API Keys:** Environment variables

---

## 📊 MONITORING & LOGGING

### Logging Strategy

**Log Levels:**
- ERROR - Lỗi nghiêm trọng cần xử lý
- WARN - Cảnh báo
- INFO - Thông tin hoạt động
- DEBUG - Debug chi tiết

**Log Files:**
```
EBANKING/BACKEND/logs/admintool-dev.log
EBANKING/BACKEND/adminTool/logs/admintool-dev.log.*
```

**Log Rotation:** Automatic daily rotation with compression

### Monitoring

**Prometheus + Grafana:**
- Service health metrics
- Response time
- Error rates
- Database connection pool
- Kafka lag monitoring
- JVM metrics

---

## 🚀 CI/CD PIPELINE

### Jenkins Pipeline

**Jenkinsfile location:** `EBANKING/BACKEND/Jenkinsfile`

**Pipeline Stages:**
1. **Checkout** - Clone code from Git
2. **Build** - Maven clean install
3. **Test** - Run unit tests
4. **SonarQube** - Code quality scan
5. **Docker Build** - Build Docker images
6. **Deploy** - Deploy to environments

**Environments:**
- Development
- Staging
- Production

---

## 🎯 TÍNH NĂNG CHÍNH CỦA HỆ THỐNG

### 1. Quản lý Authentication & Authorization ✅
- Đăng ký/Đăng nhập với JWT
- Refresh token tự động
- Role-based access control
- Session management với Redis
- Multi-device support

### 2. Quản lý User Profile ✅
- CRUD user profile
- Upload avatar
- Update thông tin cá nhân
- View transaction history
- eKYC status tracking

### 3. eKYC - Electronic Know Your Customer ✅
- Tích hợp FPT AI SDK
- OCR - Quét CCCD/CMND tự động
- Liveness Detection - Phát hiện khuôn mặt thật
- Face Matching - So khớp khuôn mặt với ảnh CCCD
- Session-based workflow
- WebView integration cho mobile

### 4. Transaction Management ✅
- Chuyển tiền nội bộ
- Lịch sử giao dịch
- Transaction validation
- Real-time transaction updates qua Kafka
- Transaction approval workflow (admin)

### 5. Chat & Communication ✅
- Real-time chat giữa users
- WebSocket cho instant messaging
- Chat history persistence
- Online/offline status

### 6. AI Chatbot ✅
- Google Gemini AI integration
- Context-aware conversations
- Function calling:
  - Lấy thông tin user
  - Query transaction history
  - Check account balance
- Natural language processing

### 7. Multi-channel Notifications ✅
- **Email notifications:**
  - Welcome email
  - Transaction notification
  - eKYC status updates
  - Password reset
- **Push notifications (Firebase):**
  - Personal notifications
  - Bulk notifications
  - Real-time alerts
- **WebSocket notifications:**
  - Live updates

### 8. Admin Dashboard ✅
- User management
- Transaction approval
- System statistics
- Logs viewing
- Configuration management

---

## 📈 PERFORMANCE & SCALABILITY

### Performance Optimizations

**Caching Strategy:**
- Redis caching cho user profiles
- Redis session storage
- Transaction caching
- Response caching

**Database Optimization:**
- Indexed columns cho queries nhanh
- Connection pooling (HikariCP)
- Query optimization
- Database replication ready

**Async Processing:**
- Kafka event-driven cho non-blocking operations
- Email sending async
- Notification sending async
- Transaction processing async

### Scalability

**Horizontal Scaling:**
- Mỗi microservice có thể scale độc lập
- Load balancing qua Nginx
- Stateless services (trừ session trong Redis)

**Vertical Scaling:**
- JVM tuning options
- Database optimization
- Redis memory management

---

## 🛠️ DEVELOPMENT SETUP

### Prerequisites

**Yêu cầu cài đặt:**
- JDK 17+
- Maven 3.8+
- Docker & Docker Compose
- Node.js 18+ & npm
- MySQL 8.0
- Redis 7
- Git

### Backend Setup

```bash
# Clone repository
git clone <repository-url>
cd EBANKING/BACKEND

# Build all services
mvn clean install

# Run specific service
cd authService
mvn spring-boot:run

# Or use Docker Compose
docker-compose up -d
```

### Frontend Setup

```bash
cd EBANKING/FRONTEND/ebanking-fe

# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build
```

### Environment Variables

**Backend (.env files):**
```
# Database
DB_HOST=localhost
DB_PORT=3306
DB_NAME=DB_USER_SERVICE
DB_USERNAME=root
DB_PASSWORD=root@123

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# Kafka
KAFKA_BOOTSTRAP_SERVERS=localhost:9092

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRATION=900000

# FPT AI
FPT_AI_API_KEY=your-api-key
FPT_AI_API_URL=https://api.fpt.ai

# Firebase
FIREBASE_PROJECT_ID=your-project-id
FIREBASE_CREDENTIALS_PATH=/path/to/credentials.json

# Google Gemini
GEMINI_API_KEY=your-gemini-api-key

# Email
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-password
```

---

## 📚 TÀI LIỆU THAM KHẢO

### API Documentation

**Swagger UI:**
- AdminTool: `http://localhost:8080/swagger-ui.html`
- Các service khác có Swagger tương tự

**Postman Collections:**
- `EBANKING/BACKEND/adminTool/Transaction_Request_API.postman_collection.json`

### Service-Specific Docs

- **eKYC Service:** `EBANKING/BACKEND/ekycService/README.md`
- **Firebase Service:** `EBANKING/BACKEND/firebaseService/PERSONAL_NOTIFICATION_API.md`
- **Chatbot Service:** `EBANKING/BACKEND/chatbotService/USER_FUNCTION_GUIDE.md`

### Technical Docs

- Spring Boot Documentation: https://spring.io/projects/spring-boot
- Spring Cloud: https://spring.io/projects/spring-cloud
- gRPC Java: https://grpc.io/docs/languages/java/
- Kafka Documentation: https://kafka.apache.org/documentation/
- React Documentation: https://react.dev/

---

## 🔄 WORKFLOW EXAMPLES

### User Registration Flow

```
1. User nhập thông tin → POST /api/auth/register
2. authService tạo user → Save to MySQL
3. authService gửi event → Kafka topic: user.created
4. emailService consume event → Gửi welcome email
5. Return JWT tokens → User login tự động
```

### Transaction Flow

```
1. User request chuyển tiền → POST /api/transactions
2. transactionService validate → Check balance, user info
3. Publish event → Kafka: transaction.initiated
4. Process transaction → Update database
5. Publish event → Kafka: transaction.completed
6. firebaseService consume → Gửi push notification
7. emailService consume → Gửi email confirmation
8. WebSocket broadcast → Real-time update to Frontend
```

### eKYC Flow (SDK Integration)

```
1. Frontend: Tạo session → POST /api/ekyc/sessions?userId=123
2. Backend: Return sessionId + status=INITIATED
3. Frontend: Init SDK → POST /api/ekyc/sessions/{sessionId}/init-sdk
4. Backend: Return SDK config (apiKey, callbackUrl, sessionToken)
5. Frontend: Mở FPT AI WebView với config
6. User thực hiện eKYC trong WebView
7. FPT AI callback OCR → POST /api/ekyc/sessions/{sessionId}/ocr-callback
8. Backend: Save OCR data, update status=OCR_COMPLETED
9. FPT AI callback Liveness → POST /api/ekyc/sessions/{sessionId}/liveness-callback
10. Backend: Save liveness data, update status=LIVENESS_COMPLETED
11. FPT AI callback Face Match → POST /api/ekyc/sessions/{sessionId}/face-match-callback
12. Backend: Save face match, update status=COMPLETED
13. Frontend: Check status → GET /api/ekyc/sessions/{sessionId}
14. Backend: Return status=COMPLETED
15. Frontend: Navigate to success screen
```

---

## 🎓 KIẾN THỨC YÊU CẦU

### Backend Developer

**Required:**
- Java 17+ và OOP
- Spring Boot, Spring Security
- RESTful API design
- MySQL, JPA/Hibernate
- Docker basics
- Git version control

**Nice to have:**
- Microservices architecture
- gRPC, Protocol Buffers
- Apache Kafka
- Redis caching
- CI/CD với Jenkins

### Frontend Developer

**Required:**
- React 19+ và Hooks
- TypeScript
- HTML5, CSS3
- RESTful API integration
- State management (Zustand/Redux)
- Git version control

**Nice to have:**
- Vite build tool
- TailwindCSS
- WebSocket client
- React Query/Axios
- i18n internationalization

### DevOps Engineer

**Required:**
- Docker & Docker Compose
- Linux command line
- Nginx configuration
- CI/CD pipelines

**Nice to have:**
- Kubernetes
- Monitoring (Prometheus/Grafana)
- Log aggregation (ELK stack)
- Cloud platforms (AWS/Azure/GCP)

---

## 📞 CONTACT & SUPPORT

### Project Structure

```
EBANKING/
├── BACKEND/
│   ├── adminTool/          # Port 8080
│   ├── authService/        # Port 8081
│   ├── userService/        # Port 8082
│   ├── transactionService/ # Port 8083
│   ├── chatService/        # Port 8084
│   ├── chatbotService/     # Port 8085
│   ├── ekycService/        # Port 8086
│   ├── emailService/       # Port 8087
│   ├── firebaseService/    # Port 8088
│   ├── socket/             # Port 8089
│   ├── docker-compose.yml
│   ├── pom.xml
│   └── Jenkinsfile
│
└── FRONTEND/
    └── ebanking-fe/
        ├── src/
        ├── package.json
        └── vite.config.ts
```

---

## ✅ KẾT LUẬN

Hệ thống E-Banking này là một dự án **production-ready** với kiến trúc microservices hiện đại, sử dụng các công nghệ tiên tiến:

**Điểm mạnh:**
- ✅ Kiến trúc Microservices dễ scale và maintain
- ✅ Event-driven với Kafka cho performance cao
- ✅ gRPC cho inter-service communication nhanh
- ✅ Redis caching cho response time tốt
- ✅ Real-time features với WebSocket
- ✅ AI Chatbot với Google Gemini
- ✅ eKYC hiện đại với FPT AI
- ✅ Multi-channel notifications
- ✅ Docker containerization
- ✅ CI/CD ready với Jenkins
- ✅ Monitoring với Prometheus/Grafana
- ✅ API documentation với Swagger

**Tech Stack Summary:**
- **Backend:** Java 17, Spring Boot 3.x, Spring Cloud, gRPC, Kafka
- **Frontend:** React 19, TypeScript, Vite, TailwindCSS
- **Database:** MySQL 8.0, Redis 7
- **Infrastructure:** Docker, Nginx, Eureka, Zookeeper
- **CI/CD:** Jenkins, Docker Compose
- **Monitoring:** Prometheus, Grafana
- **3rd Party:** FPT AI, Firebase, Google Gemini

Đây là một hệ thống banking hoàn chỉnh, có thể triển khai thực tế và scale cho hàng triệu users.

---

**Document Version:** 1.0  
**Last Updated:** 2026  
**Maintained by:** E-Banking Development Team
