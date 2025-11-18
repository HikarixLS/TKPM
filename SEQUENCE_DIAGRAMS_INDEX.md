# 📊 AppleStore Backend - Sequence Diagrams Index

> **Tài liệu tổng hợp các sơ đồ tuần tự (Sequence Diagrams) cho AppleStore Backend Project**  
> Minh họa luồng tương tác giữa các thành phần trong hệ thống theo kiến trúc 3 lớp

---

## 📁 Danh sách Sequence Diagrams

### 1. 🔐 Authentication Flow
**File:** `SEQ_01_Authentication.drawio`

**Mô tả:** Sơ đồ tuần tự cho các chức năng xác thực và phân quyền người dùng

**Các luồng bao gồm:**

#### 1.1. User Registration (Đăng ký tài khoản)
- **Actors:** Client, AuthController, UserService, PasswordEncoder, UserRepository, Database
- **Luồng chính:**
  1. Client gửi POST request với thông tin đăng ký (fullName, email, password, phoneNumber, address)
  2. AuthController nhận request và gọi UserService.registerUser()
  3. UserService kiểm tra email đã tồn tại chưa qua UserRepository
  4. Mã hóa password bằng PasswordEncoder (BCrypt)
  5. Lưu User entity vào database qua UserRepository
  6. Trả về UserDTO (không chứa password) với status 201 Created

**Patterns áp dụng:**
- ✅ MVC Pattern (Controller → Service → Repository)
- ✅ DTO Pattern (chuyển Entity → DTO)
- ✅ Repository Pattern (truy cập database)

#### 1.2. User Login (Đăng nhập)
- **Actors:** Client, AuthController, UserService, AuthenticationManager, CustomUserDetailsService, UserRepository, JwtTokenProvider
- **Luồng chính:**
  1. Client gửi POST request với email và password
  2. AuthController gọi UserService.loginUser()
  3. UserService sử dụng AuthenticationManager để xác thực
  4. AuthenticationManager gọi CustomUserDetailsService.loadUserByUsername()
  5. CustomUserDetailsService lấy thông tin user từ UserRepository
  6. So sánh password đã mã hóa (PasswordEncoder.matches())
  7. Tạo JWT token qua JwtTokenProvider.generateToken()
  8. Trả về AuthResponseDTO chứa token và thông tin user

**Security Features:**
- ✅ JWT Token Authentication
- ✅ Password Encryption (BCrypt)
- ✅ Spring Security Integration

#### 1.3. Get Current User (Lấy thông tin user đang đăng nhập)
- **Actors:** Client, JwtAuthenticationFilter, JwtTokenProvider, AuthController, UserService, UserRepository
- **Luồng chính:**
  1. Client gửi GET request kèm header Authorization: Bearer <token>
  2. JwtAuthenticationFilter intercept và validate token
  3. JwtTokenProvider.validateToken() kiểm tra token hợp lệ
  4. Extract userId từ JWT token
  5. Set user vào SecurityContext
  6. AuthController gọi UserService.getCurrentUser()
  7. UserService lấy user từ SecurityContext và truy vấn database
  8. Trả về UserDTO với status 200 OK

**JWT Flow:**
- ✅ Token Validation
- ✅ SecurityContext Management
- ✅ Stateless Authentication

---

### 2. 📦 Product Management Flow
**File:** `SEQ_02_Product_Management.drawio`

**Mô tả:** Sơ đồ tuần tự cho các chức năng quản lý sản phẩm

**Các luồng bao gồm:**

#### 2.1. View All Products (Xem danh sách sản phẩm)
- **Actors:** Client, ProductController, ProductService, ProductRepository, Database
- **Luồng chính:**
  1. Client gửi GET /api/products?page=0&size=20
  2. ProductController nhận request và gọi ProductService.getAllProducts(pageable)
  3. ProductService gọi ProductRepository.findAll(pageable)
  4. Repository thực hiện query SELECT với LIMIT
  5. Convert Page<Product> sang Page<ProductDTO> bằng ModelMapper
  6. Trả về danh sách ProductDTO với status 200 OK

**Features:**
- ✅ Pagination Support (Spring Data JPA)
- ✅ DTO Mapping (ModelMapper)

#### 2.2. Search Products (Tìm kiếm sản phẩm)
- **Actors:** Client, ProductController, ProductService, ProductRepository, Database
- **Luồng chính:**
  1. Client gửi GET /api/products/search?keyword=iPhone
  2. ProductService gọi ProductRepository với custom query
  3. Repository sử dụng JPQL: `WHERE name LIKE %keyword% OR description LIKE %keyword%`
  4. Trả về danh sách sản phẩm phù hợp

**Search Features:**
- ✅ Full-text search (name + description)
- ✅ Case-insensitive matching

#### 2.3. Create Product - Admin Only (Thêm sản phẩm mới)
- **Actors:** Admin Client, JwtAuthenticationFilter, ProductController, ProductService, CategoryRepository, ProductRepository, Database
- **Luồng chính:**
  1. Admin gửi POST /api/products với JWT token
  2. JwtAuthenticationFilter validate token và check ADMIN role
  3. @PreAuthorize("hasRole('ADMIN')") annotation kiểm tra quyền
  4. ProductService validate categoryId qua CategoryRepository
  5. Tạo Product entity và set category
  6. Lưu vào database qua ProductRepository
  7. Trả về ProductDTO với status 201 Created

**Security:**
- ✅ Role-based Access Control (RBAC)
- ✅ @PreAuthorize annotation

#### 2.4. Update Product - Admin Only (Cập nhật sản phẩm)
- **Actors:** Admin Client, ProductController, ProductService, ProductRepository, Database
- **Luồng chính:**
  1. Admin gửi PUT /api/products/{id} với dữ liệu cập nhật
  2. ProductService tìm Product entity theo id
  3. Cập nhật các fields (name, price, stockQuantity, description, etc.)
  4. Save updated entity vào database
  5. Trả về ProductDTO đã cập nhật

**Update Strategy:**
- ✅ Partial Update Support
- ✅ Optimistic Locking (nếu cần)

---

### 3. 🛒 Cart & Order Processing Flow
**File:** `SEQ_03_Cart_Order.drawio`

**Mô tả:** Sơ đồ tuần tự cho giỏ hàng và xử lý đơn hàng với Observer Pattern

**Các luồng bao gồm:**

#### 3.1. Add to Cart (Thêm sản phẩm vào giỏ hàng)
- **Actors:** Customer Client, CartController, CartService, ProductRepository, CartItemRepository, Database
- **Luồng chính:**
  1. Customer gửi POST /api/cart với {productId, quantity}
  2. CartService lấy current user từ SecurityContext
  3. Validate product tồn tại và còn hàng (stock availability)
  4. Kiểm tra CartItem đã tồn tại cho user + product chưa
  5. Nếu chưa có: tạo CartItem mới
  6. Nếu đã có: update quantity (increment)
  7. Lưu vào database qua CartItemRepository
  8. Trả về CartItemDTO với status 201 Created

**Business Logic:**
- ✅ Stock Validation
- ✅ Duplicate Item Handling (merge quantities)

#### 3.2. Create Order with Observer Pattern (Tạo đơn hàng với thông báo sự kiện)
- **Actors:** Customer Client, OrderController, OrderService, CartItemRepository, OrderRepository, EventPublisher, EmailObserver, NotificationObserver
- **Luồng chính:**
  1. Customer gửi POST /api/orders với {shippingAddress, paymentMethod}
  2. OrderService lấy current user và cart items từ CartItemRepository
  3. Generate unique order number (e.g., ORD-20241118-001)
  4. Calculate total amount từ cart items
  5. Create Order entity với OrderItems
  6. Save Order vào database qua OrderRepository
  7. Clear cart items của user (deleteAll)
  8. **🔔 Publish OrderCreatedEvent qua EventPublisher** (Observer Pattern)
  9. EventPublisher notify tất cả observers:
     - **EmailObserver:** Send email confirmation đến customer
     - **NotificationObserver:** Create in-app notification
  10. Trả về OrderDTO với status 201 Created

**Observer Pattern Benefits:**
- ✅ **Loose Coupling:** OrderService không phụ thuộc vào Observers
- ✅ **Extensibility:** Dễ dàng thêm observers mới (SMS, Push Notification)
- ✅ **Async Processing:** Events được xử lý bất đồng bộ
- ✅ **Single Responsibility:** Mỗi observer xử lý 1 nhiệm vụ cụ thể

#### 3.3. Update Order Status - Admin (Cập nhật trạng thái đơn hàng)
- **Actors:** Admin Client, OrderController, OrderService, OrderRepository, EventPublisher, Observers
- **Luồng chính:**
  1. Admin gửi PUT /api/orders/{id}/status?status=SHIPPED
  2. OrderService tìm Order entity theo id
  3. Update order status (PENDING → PROCESSING → SHIPPED → DELIVERED)
  4. Update updatedAt timestamp
  5. Save order vào database
  6. **🔔 Publish OrderStatusChangedEvent** (Observer Pattern)
  7. Observers gửi thông báo cập nhật cho customer
  8. Trả về OrderDTO đã cập nhật

**Order Status Flow:**
```
PENDING → PROCESSING → SHIPPED → DELIVERED
                    ↘
                    CANCELLED
```

---

### 4. ⭐ Review System Flow
**File:** `SEQ_04_Review_System.drawio`

**Mô tả:** Sơ đồ tuần tự cho hệ thống đánh giá sản phẩm với Observer Pattern

**Các luồng bao gồm:**

#### 4.1. Write Product Review with Observer Pattern (Viết đánh giá sản phẩm)
- **Actors:** Customer Client, ReviewController, ReviewService, ProductRepository, ReviewRepository, EventPublisher, EmailObserver, NotificationObserver
- **Luồng chính:**
  1. Customer gửi POST /api/reviews với {productId, rating, comment}
  2. ReviewService lấy current user từ SecurityContext
  3. Validate product tồn tại qua ProductRepository
  4. Kiểm tra user đã review product này chưa (1 user chỉ review 1 lần)
  5. Create Review entity với user, product, rating, comment
  6. Save review vào database qua ReviewRepository
  7. Update product's average rating và review count
  8. **🔔 Publish ReviewCreatedEvent qua EventPublisher** (Observer Pattern)
  9. EventPublisher notify observers:
     - **EmailObserver:** Gửi email thông báo cho admin/product owner
     - **NotificationObserver:** Tạo in-app notification "New review on your product"
  10. Trả về ReviewDTO với status 201 Created

**Observer Pattern trong Review System:**

| Observer | Nhiệm vụ |
|----------|----------|
| **EmailObserver** | Gửi email thông báo cho admin/owner về đánh giá mới |
| **NotificationObserver** | Tạo in-app notification cho người dùng liên quan |
| **AnalyticsObserver** | Thu thập thống kê đánh giá (có thể thêm) |
| **ModerationObserver** | Kiểm duyệt nội dung tự động (có thể thêm) |

**Review System Benefits:**
- ✅ Tách biệt logic xử lý review và gửi thông báo
- ✅ Dễ dàng thêm/xóa observers mà không ảnh hưởng ReviewService
- ✅ Xử lý bất đồng bộ - không làm chậm API response
- ✅ Automatic Product Rating Update

---

## 🎨 Color Coding trong Diagrams

| Component | Color | Description |
|-----------|-------|-------------|
| **Client** | Blue (#dae8fc) | Frontend/Client applications |
| **Controllers** | Green (#d5e8d4) | REST API Controllers (Presentation Layer) |
| **Services** | Orange (#ffe6cc) | Business Logic Layer |
| **Repositories** | Pink (#f8cecc) | Data Access Layer |
| **Database** | Gray (#f5f5f5) | MySQL Database |
| **Security Components** | Purple (#e1d5e7) | JWT, Filters, Auth components |
| **Event/Observer** | Purple (#e1d5e7) | Observer Pattern components |
| **Other Components** | Yellow (#fff2cc) | Supporting components |

---

## 🏗️ Kiến trúc 3 lớp (3-Layer Architecture)

Tất cả sequence diagrams đều tuân theo kiến trúc 3 lớp:

```
┌─────────────────────────────────────────┐
│   Presentation Layer (Controllers)     │  ← REST API Endpoints
│   - AuthController                     │
│   - ProductController                  │
│   - OrderController                    │
│   - CartController                     │
│   - ReviewController                   │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│   Business Logic Layer (Services)      │  ← Business Rules & Logic
│   - UserService                        │
│   - ProductService                     │
│   - OrderService                       │
│   - CartService                        │
│   - ReviewService                      │
│   ┌──────────────────────┐             │
│   │  Observer Pattern    │             │  ← Event Notification
│   │  - EventPublisher    │             │
│   │  - EmailObserver     │             │
│   │  - NotificationObs   │             │
│   └──────────────────────┘             │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│   Data Access Layer (Repositories)      │  ← Database Operations
│   - UserRepository                      │
│   - ProductRepository                   │
│   - OrderRepository                     │
│   - CartItemRepository                  │
│   - ReviewRepository                    │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│         MySQL Database                  │
└─────────────────────────────────────────┘
```

---

## 📋 Design Patterns được áp dụng

### 1. MVC Pattern (Model-View-Controller)
- **Model:** Entity classes (User, Product, Order, etc.)
- **View:** REST API responses (DTOs)
- **Controller:** Controller classes xử lý HTTP requests

### 2. Observer Pattern
- **Subject:** OrderService, ReviewService
- **Observers:** EmailObserver, NotificationObserver
- **Event:** OrderCreatedEvent, OrderStatusChangedEvent, ReviewCreatedEvent
- **Publisher:** EventPublisher

**Ưu điểm:**
- Loose coupling giữa business logic và notification logic
- Dễ dàng thêm/xóa observers
- Async processing không block main flow

### 3. DTO Pattern (Data Transfer Object)
- Tách biệt Entity và Data Transfer
- Không expose sensitive data (e.g., password)
- Customize response structure

### 4. Repository Pattern
- Abstraction layer cho data access
- Sử dụng Spring Data JPA
- Dễ dàng switch database implementation

### 5. Dependency Injection
- Constructor injection với @RequiredArgsConstructor (Lombok)
- Inversion of Control (IoC)
- Testability

---

## 🔐 Security Features

### JWT Authentication Flow
1. **Login:** User login → Generate JWT token
2. **Authenticated Request:** Include token in Authorization header
3. **Validation:** JwtAuthenticationFilter validates token
4. **Authorization:** @PreAuthorize checks roles/permissions

### Role-Based Access Control (RBAC)
- **CUSTOMER role:** Create orders, write reviews, manage cart
- **ADMIN role:** Manage products, update order status, manage categories

---

## 🚀 Sử dụng Sequence Diagrams

### Mở file trong Draw.io:
1. Truy cập [app.diagrams.net](https://app.diagrams.net)
2. Chọn **Open Existing Diagram**
3. Chọn file `.drawio` tương ứng
4. Xem và chỉnh sửa diagram

### Hoặc sử dụng Draw.io Desktop:
1. Tải và cài đặt Draw.io Desktop
2. Mở file `.drawio` trực tiếp
3. Export sang PNG, SVG, PDF nếu cần

---

## 📚 Tài liệu liên quan

- [API_DOCUMENTATION.md](AppleStore-Backend/API_DOCUMENTATION.md) - Tài liệu API endpoints chi tiết
- [DESIGN_PATTERNS.md](AppleStore-Backend/DESIGN_PATTERNS.md) - Chi tiết về Design Patterns và SOLID Principles
- [README.md](AppleStore-Backend/README.md) - Tổng quan về project
- [USE_CASE_FILES_INDEX.md](USE_CASE_FILES_INDEX.md) - Danh sách Use Case Specifications

---

## 📝 Notes

- Tất cả sequence diagrams được vẽ theo chuẩn UML 2.0
- Sử dụng lifelines để thể hiện object lifecycle
- Solid arrows (→) cho synchronous calls
- Dashed arrows (⇢) cho return values
- Messages được đánh số theo thứ tự thời gian

---

**Created:** November 18, 2025  
**Author:** TKPM Team  
**Project:** AppleStore Backend - Spring Boot REST API
