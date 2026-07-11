# Tổng Quan Dự Án - Coding Practice Platform

## 📋 Mô Tả Dự Án

Đây là một nền tảng luyện tập lập trình (Online Judge) cho phép người dùng viết, biên dịch và chạy code Java trực tuyến với hệ thống test cases tự động. Dự án được xây dựng với mục đích hỗ trợ học tập và thực hành nhiều ngôn ngữ lập trình (hiện tại tập trung vào Java).

## 🎯 Chức Năng Chính

### 1. Biên Dịch Code Java
- **Endpoint**: `POST /java/compile/`
- Biên dịch code Java được gửi lên từ người dùng
- Trả về kết quả biên dịch hoặc thông báo lỗi chi tiết
- Thực hiện trong môi trường Docker để đảm bảo an toàn

### 2. Chạy Code Đơn Giản
- **Endpoint**: `POST /java/compile/run`
- Biên dịch và chạy code với input mẫu đơn giản
- Hiển thị output của chương trình
- Kiểm tra nhanh tính đúng đắn của code

### 3. Chạy Code Với Test Cases
- **Endpoint**: `POST /java/compile/runWithTestcases`
- Chạy code với nhiều test cases được định nghĩa trước
- So sánh output thực tế với expected output
- Hiển thị kết quả chi tiết cho từng test case (pass/fail)

### 4. Quản Lý Challenges (Bài Tập)
- **Endpoint**: `GET /java/compile/challenge/{id}`
- Lưu trữ và quản lý các bài tập lập trình
- Mỗi challenge bao gồm:
  - Nội dung đề bài (content)
  - Template code ban đầu
  - Input/output mẫu đơn giản
  - Danh sách test cases để kiểm tra

### 5. Xác Thực OAuth2 với GitHub
- **Endpoint**: `/oauth/github/authorize`, `/oauth/github/callback`
- Đăng nhập bằng tài khoản GitHub
- Quản lý thông tin người dùng thông qua OAuth2

## 🏗️ Kiến Trúc Hệ Thống

### Kiến Trúc Tổng Thể
```
┌─────────────────┐
│  React Client   │
│ (localhost:3000)│
└────────┬────────┘
         │ HTTP/REST API
         ▼
┌─────────────────────────────┐
│   Spring Boot Backend       │
│  - Controllers              │
│  - Services                 │
│  - Security (OAuth2)        │
└────────┬────────────────────┘
         │
    ┌────┴────┬────────────┐
    ▼         ▼            ▼
┌─────────┐ ┌──────────┐ ┌──────────┐
│  MySQL  │ │  Docker  │ │  GitHub  │
│Database │ │Container │ │  OAuth   │
└─────────┘ └──────────┘ └──────────┘
```

### Kiến Trúc Phân Lớp (Layered Architecture)

#### 1. **Presentation Layer (Controllers)**
```
controllers/
├── javaCode/
│   └── JavaCompileController.java  - API endpoints cho compile/run code
└── githubOauth/
    └── Oauth2GithubController.java - OAuth2 authentication
```

#### 2. **Business Logic Layer (Services)**
```
services/
├── ChallengeService.java           - Quản lý challenges và test cases
├── javaCoding/
│   ├── JavaCompileService.java     - Logic biên dịch code
│   ├── JavaRunCodeService.java     - Logic chạy code
│   ├── JavaBaseService.java        - Base service cho Java operations
│   └── threads/                    - Xử lý bất đồng bộ với threads
│       ├── ThreadForJavaCompileCode.java
│       ├── ThreadsForJavaRunCode.java
│       └── ThreadRunWithTestCases.java
└── dockerService/
    ├── DockerBaseService.java      - Base Docker operations
    └── DockerServiceForJava.java   - Docker commands cho Java
```

#### 3. **Data Access Layer (Repositories)**
```
repository/
├── ChallengeRepository.java        - CRUD operations cho challenges
└── TestCaseRepository.java         - CRUD operations cho test cases
```

#### 4. **Data Layer (Entities)**
```
entity/
├── Challenge.java                  - Entity cho bài tập
├── TestCase.java                   - Entity cho test cases
└── GithubUser.java                 - Entity cho user info từ GitHub
```

### Kiến Trúc Xử Lý Code

```
User Submit Code
      ↓
[Controller nhận request]
      ↓
[Tạo Thread xử lý bất đồng bộ]
      ↓
[JavaBaseService chuẩn bị file]
      ↓
[Copy file vào Docker Container]
      ↓
[DockerServiceForJava thực thi lệnh]
      ↓
[Compile code trong container]
      ↓
[Run code với test cases]
      ↓
[Thu thập kết quả]
      ↓
[Xóa file tạm trong container]
      ↓
[Trả kết quả về client]
```

## 🛠️ Công Nghệ Sử Dụng

### Backend Framework
- **Spring Boot 3.4.4**: Framework chính
- **Java 21**: Ngôn ngữ lập trình
- **Maven**: Build tool và dependency management

### Database
- **MySQL 8**: Cơ sở dữ liệu quan hệ
- **Spring Data JPA**: ORM framework
- **Hibernate**: JPA implementation

### Security & Authentication
- **Spring Security**: Framework bảo mật
- **OAuth2 Client**: Xác thực với GitHub
- **OAuth2 Authorization Server**: OAuth2 server implementation

### Container & Deployment
- **Docker**: Containerization để chạy code an toàn
  - Image: `eclipse-temurin:21-jdk`
  - Container name: `jdk21-container`
- **Docker Compose**: Quản lý container orchestration

### Utilities & Libraries
- **Lombok**: Giảm boilerplate code
- **MapStruct**: Object mapping giữa Entity và DTO
- **Jackson**: JSON processing
- **SLF4J + Logback**: Logging framework

### Testing
- **JUnit Jupiter**: Unit testing framework
- **Spring Boot Test**: Testing utilities
- **TestNG**: Alternative testing framework

### Development Tools
- **Maven Wrapper**: mvnw/mvnw.cmd để không cần cài Maven global

## 📊 Cơ Sở Dữ Liệu

### Schema Design

```sql
┌──────────────────────┐
│     Challenge        │
├──────────────────────┤
│ id (PK)              │
│ content (LONGTEXT)   │
│ template (LONGTEXT)  │
│ simpleInput          │
│ simpleOutput         │
└──────────┬───────────┘
           │ 1
           │
           │ N
┌──────────┴───────────┐
│     TestCase         │
├──────────────────────┤
│ id (PK)              │
│ input                │
│ output               │
│ challenge_id (FK)    │
└──────────────────────┘
```

### Mối Quan Hệ
- **Challenge** ↔ **TestCase**: One-to-Many
  - Một challenge có nhiều test cases
  - Sử dụng `@OneToMany` và `@ManyToOne` mapping
  - Cascade ALL để tự động quản lý test cases khi thao tác với challenge

## 🔒 Bảo Mật

### Authentication
- OAuth2 với GitHub để xác thực người dùng
- Không yêu cầu authentication cho compile endpoints (permitAll) - có thể cần review

### Code Execution Security
- **Docker Isolation**: Mỗi code execution chạy trong Docker container riêng biệt
- **File System Isolation**: Code được lưu trong `/app` directory trong container
- **Automatic Cleanup**: File được xóa ngay sau khi compile/run xong

### CORS Configuration
- Cho phép request từ `http://localhost:3000` (React client)
- Các method được phép: GET, POST, PUT, DELETE, OPTIONS
- Allow credentials: true

## 📁 Cấu Trúc Thư Mục Chính

```
├── src/main/java/myself/programing/coding/
│   ├── config/              - Cấu hình (Security, CORS)
│   ├── consts/              - Constants
│   ├── controllers/         - REST Controllers
│   ├── dto/                 - Data Transfer Objects
│   ├── entity/              - JPA Entities
│   ├── enums/               - Enumerations
│   ├── exception/           - Custom Exceptions
│   ├── mapper/              - MapStruct Mappers
│   ├── model/               - Domain Models
│   ├── oauth2/              - OAuth2 implementation
│   ├── repository/          - JPA Repositories
│   ├── services/            - Business Logic
│   └── utils/               - Utility classes
├── src/main/resources/
│   ├── application.properties  - Application configuration
│   └── conf/
│       └── logback.xml      - Logging configuration
├── databases/               - Database configs
│   ├── docker-compose.yml
│   └── my.cnf
├── logs/                    - Application logs
├── fileStorage/             - Temporary code storage
└── docker-compose.yml       - Docker orchestration
```

## 🔄 Luồng Xử Lý Chính

### 1. Compile Code Flow
```
Client → POST /java/compile/
    ↓
JavaCompileController.compile()
    ↓
ThreadForJavaCompileCode.compile()
    ↓
JavaBaseService.createAndCopyFile()
    ↓
DockerServiceForJava.genCompileFileJavaCmd()
    ↓
DockerBaseService.executeDockerCommandHasResult()
    ↓
Return result hoặc error
    ↓
DockerServiceForJava.deleteFile()
```

### 2. Run With Test Cases Flow
```
Client → POST /java/compile/runWithTestcases
    ↓
JavaCompileController.runWithTests()
    ↓
ThreadRunWithTestCases.runWithTest()
    ↓
Compile code
    ↓
Foreach test case:
    ↓
    Run code với input
    ↓
    So sánh output với expected
    ↓
    Thu thập kết quả (pass/fail)
    ↓
Return List<RunWithTestCasesDto>
```

## 🚀 Cách Chạy Dự Án

### Prerequisites
- Java 21
- Maven 3.x
- Docker & Docker Compose
- MySQL 8
- GitHub OAuth App (client ID & secret)

### Các Bước

1. **Khởi động Docker container cho Java runtime**
```bash
docker-compose up -d
```

2. **Cấu hình database**
- Tạo database: `code_gym`
- Update credentials trong `application.properties` nếu cần

3. **Cấu hình GitHub OAuth**
- Tạo GitHub OAuth App
- Update `github_client` và `github_secret_key` trong `application.properties`

4. **Build và chạy application**
```bash
./mvnw clean install
./mvnw spring-boot:run
```

5. **Application sẽ chạy tại**
- Backend: `http://localhost:8080`
- Frontend (React): `http://localhost:3000`

## 📝 API Endpoints

### Java Compilation
- `POST /java/compile/` - Compile code
- `POST /java/compile/run` - Compile và run với simple test
- `POST /java/compile/runWithTestcases` - Run với full test cases

### Challenge Management
- `GET /java/compile/challenge/{id}` - Lấy thông tin challenge

### OAuth2
- `GET /oauth/github/authorize` - Bắt đầu OAuth flow
- `GET /oauth/github/callback` - OAuth callback endpoint

## 🎨 Design Patterns Được Sử Dụng

1. **Layered Architecture**: Phân tách rõ ràng giữa các layer
2. **Repository Pattern**: Truy xuất dữ liệu thông qua repositories
3. **DTO Pattern**: Truyền dữ liệu giữa các layer bằng DTOs
4. **Service Pattern**: Business logic tập trung trong services
5. **Template Method Pattern**: JavaBaseService làm base cho các service khác
6. **Builder Pattern**: Sử dụng Lombok @Builder cho entities và DTOs
7. **Dependency Injection**: Spring IoC container quản lý dependencies

## 📊 Logging

- **Framework**: SLF4J + Logback
- **Config file**: `src/main/resources/conf/logback.xml`
- **Log locations**: 
  - `logs/coding_application.log` - Main application logs
  - `logs/coding_applicationError.log` - Error logs
  - `logs/java_controller/` - Controller specific logs
  - `logs/java_service/` - Service specific logs
  - `logs/docker_base_service/` - Docker operation logs

## 🔮 Mở Rộng Trong Tương Lai

- Hỗ trợ thêm ngôn ngữ lập trình khác (Python, C++, JavaScript)
- Thêm hệ thống ranking và leaderboard
- Real-time code collaboration
- Code review và discussion features
- Performance optimization metrics
- Advanced test case management
- User dashboard và progress tracking

## 👥 Đối Tượng Sử Dụng

- Sinh viên học lập trình
- Người mới bắt đầu với Java
- Người muốn luyện tập thuật toán
- Giáo viên tạo bài tập cho học sinh
- Tổ chức thi lập trình trực tuyến

---

**Version**: 0.0.1-SNAPSHOT  
**Last Updated**: 2026
