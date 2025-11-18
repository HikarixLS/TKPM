# 📚 USE CASE SPECIFICATIONS - DRAW.IO FILES

Tài liệu Use Case Specifications đầy đủ cho AppleStore Backend - Chia thành 6 files Draw.io theo nhóm chức năng

---

## 📂 DANH SÁCH FILES

| # | File | Nhóm chức năng | Use Cases | Số lượng |
|---|------|----------------|-----------|----------|
| 1 | **UC_01_Authentication.drawio** | 🔐 Authentication & Authorization | UC1-UC3 | 3 |
| 2 | **UC_02_Products.drawio** | 📱 Product Management | UC4-UC13 | 10 |
| 3 | **UC_03_Cart.drawio** | 🛒 Cart Management | UC14-UC18 | 5 |
| 4 | **UC_04_Orders.drawio** | 📦 Order Management | UC19-UC25 | 7 |
| 5 | **UC_05_Reviews_Categories.drawio** | ⭐🏷️ Reviews & Categories | UC26-UC33 | 8 |
| 6 | **UC_06_Notifications.drawio** | 🔔 Notification System | UC34-UC36 | 3 |
| | **TỔNG CỘNG** | **6 nhóm chức năng** | **UC1-UC36** | **36** ✅ |

---

## 📋 CHI TIẾT TỪNG FILE

### 1️⃣ UC_01_Authentication.drawio (🔐 3 Use Cases)

**Nội dung:**
- **UC1: Đăng ký tài khoản** - Register with email validation, BCrypt password, Observer Pattern for welcome email
- **UC2: Đăng nhập** - Login with JWT token generation (24h expiry)
- **UC3: Xem thông tin cá nhân** - Get user profile with JWT authentication

**Màu sắc:** Xanh dương (#dae8fc)

**Công nghệ:** JWT, BCrypt, Spring Security, Observer Pattern

---

### 2️⃣ UC_02_Products.drawio (📱 10 Use Cases)

**Nội dung:**
- **UC4: Xem danh sách sản phẩm** - List products with pagination, ratings
- **UC5: Xem chi tiết sản phẩm** - Product details with category, reviews, images
- **UC6: Tìm kiếm sản phẩm** - Search by name, description (LIKE %keyword%)
- **UC7: Lọc sản phẩm theo màu sắc** - Filter by color
- **UC8: Lọc sản phẩm theo dung lượng** - Filter by storage (128GB, 256GB, 512GB, 1TB)
- **UC9: Xem sản phẩm nổi bật** - Featured products (is_featured = true)
- **UC10: Xem sản phẩm mới ra mắt** - New arrivals (is_new_arrival = true)
- **UC11: Thêm sản phẩm mới (Admin)** - Admin only: Add product with validation
- **UC12: Cập nhật sản phẩm (Admin)** - Admin only: Update product info
- **UC13: Xóa sản phẩm (Admin)** - Admin only: Delete product (check constraints)

**Màu sắc:** Vàng (#fff2cc)

**Công nghệ:** Spring Data JPA, Pagination, DTO Pattern

---

### 3️⃣ UC_03_Cart.drawio (🛒 5 Use Cases)

**Nội dung:**
- **UC14: Xem giỏ hàng** - View cart with subtotal calculation
- **UC15: Thêm sản phẩm vào giỏ hàng** - Add to cart with stock validation
- **UC16: Cập nhật số lượng trong giỏ** - Update quantity (check stock availability)
- **UC17: Xóa sản phẩm khỏi giỏ hàng** - Remove single item from cart
- **UC18: Xóa toàn bộ giỏ hàng** - Clear entire cart

**Màu sắc:** Xanh lá (#d5e8d4)

**Công nghệ:** Real-time stock validation, Subtotal calculation

---

### 4️⃣ UC_04_Orders.drawio (📦 7 Use Cases)

**Nội dung:**
- **UC19: Tạo đơn hàng** - Create order (9 steps: validate cart, check stock, create order, send email, clear cart)
- **UC20: Xem đơn hàng của tôi** - View user's orders with status
- **UC21: Xem chi tiết đơn hàng** - Order details with timeline (created, confirmed, shipped, delivered)
- **UC22: Hủy đơn hàng** - Cancel order (PENDING/CONFIRMED only), refund stock
- **UC23: Xem tất cả đơn hàng (Admin)** - Admin view all orders with filters
- **UC24: Cập nhật trạng thái đơn hàng (Admin)** - Update order status, send notification
- **UC25: Cập nhật trạng thái thanh toán (Admin)** - Update payment status (PENDING/PAID/FAILED)

**Màu sắc:** Tím (#e1d5e7)

**Công nghệ:** Observer Pattern for emails, Transaction management, Stock inventory

---

### 5️⃣ UC_05_Reviews_Categories.drawio (⭐🏷️ 8 Use Cases)

**Nội dung:**

**⭐ Reviews (UC26-UC29):**
- **UC26: Xem đánh giá sản phẩm** - View reviews with rating distribution
- **UC27: Viết đánh giá** - Create review (1-5 stars), auto-update product rating
- **UC28: Cập nhật đánh giá** - Edit own review, recalculate ratings
- **UC29: Xóa đánh giá** - Delete review (owner or admin), update product rating

**🏷️ Categories (UC30-UC33):**
- **UC30: Xem danh sách danh mục** - List categories with product count
- **UC31: Thêm danh mục (Admin)** - Admin only: Add category (unique name validation)
- **UC32: Cập nhật danh mục (Admin)** - Admin only: Update category
- **UC33: Xóa danh mục (Admin)** - Admin only: Delete empty category (check products)

**Màu sắc:** Cam (#ffe6cc) cho Reviews, Xám (#f5f5f5) cho Categories

**Công nghệ:** Aggregate functions (AVG rating), Referential integrity

---

### 6️⃣ UC_06_Notifications.drawio (🔔 3 Use Cases)

**Nội dung:**
- **UC34: Gửi email xác nhận đơn hàng** - Email on ORDER_CREATED event
- **UC35: Gửi email thông báo ship hàng** - Email on ORDER_SHIPPED event
- **UC36: Cảnh báo hết hàng tồn kho** - Admin alert on PRODUCT_OUT_OF_STOCK event

**Màu sắc:** Vàng nhạt (#fff2cc)

**Công nghệ:** 
- **Observer Pattern** (Design Pattern)
- **EventPublisher** (Subject)
- **EmailNotificationObserver** (Observer for UC34, UC35)
- **InventoryObserver** (Observer for UC36)
- **Spring Event System** (@EventListener, ApplicationEventPublisher)

**Design Principles:** Loose Coupling, Open/Closed Principle, Single Responsibility Principle

---

## 🎨 CHƯƠNG TRÌNH MÀU

| Nhóm | Màu | Hex Code |
|------|-----|----------|
| 🔐 Authentication | Xanh dương | #dae8fc |
| 📱 Products | Vàng | #fff2cc |
| 🛒 Cart | Xanh lá | #d5e8d4 |
| 📦 Orders | Tím | #e1d5e7 |
| ⭐ Reviews | Cam | #ffe6cc |
| 🏷️ Categories | Xám | #f5f5f5 |
| 🔔 Notifications | Vàng nhạt | #fff2cc |
| ✅ Success Scenario | Xanh lá | #d5e8d4 |
| ⚠️ Error Scenario | Vàng cảnh báo | #fff2cc |

---

## 📖 CÁCH SỬ DỤNG

### Bước 1: Mở file Draw.io
1. Vào trang **https://app.diagrams.net/** (Draw.io)
2. Click **"Open Existing Diagram"**
3. Chọn file `.drawio` cần xem (UC_01, UC_02, ...)

### Bước 2: Xem và chỉnh sửa
- Zoom in/out để xem chi tiết
- Click vào bất kỳ box nào để edit
- Export sang PNG, PDF, SVG nếu cần

### Bước 3: In hoặc Export
- **File → Export as → PDF** (để in)
- **File → Export as → PNG** (cho báo cáo)
- **File → Print** (in trực tiếp)

---

## 🔗 TÀI LIỆU LIÊN QUAN

| Tài liệu | Mô tả | File |
|----------|-------|------|
| **Use Case Specifications (Markdown)** | Chi tiết đầy đủ 36 use cases với flows và scenarios | `USE_CASE_SPECIFICATIONS.md` |
| **Use Case Diagram** | Sơ đồ tổng quan 36 use cases với actors và relationships | `USE_CASE_DIAGRAM.drawio` |
| **Design Patterns** | Giải thích các design patterns sử dụng (Observer, MVC, DTO, Repository) | `DESIGN_PATTERNS.md` |
| **API Documentation** | REST API endpoints với request/response examples | `API_DOCUMENTATION.md` |
| **README** | Hướng dẫn cài đặt và chạy project | `README.md` |

---

## ✅ CHECKLIST HOÀN THÀNH

- [x] **UC1-UC3**: Authentication & Authorization (3/3) ✅
- [x] **UC4-UC13**: Product Management (10/10) ✅
- [x] **UC14-UC18**: Cart Management (5/5) ✅
- [x] **UC19-UC25**: Order Management (7/7) ✅
- [x] **UC26-UC29**: Review Management (4/4) ✅
- [x] **UC30-UC33**: Category Management (4/4) ✅
- [x] **UC34-UC36**: Notification System (3/3) ✅

**TỔNG: 36/36 Use Cases** 🎉

---

## 📊 THỐNG KÊ

| Chỉ số | Giá trị |
|--------|---------|
| Tổng số Use Cases | 36 |
| Tổng số Actors | 3 (Customer, Admin, Email System) |
| Tổng số Files Draw.io | 6 |
| Use Cases phức tạp cao (≥5 bước) | 4 (UC19, UC15, UC11, UC24) |
| Use Cases có Observer Pattern | 3 (UC34, UC35, UC36) |
| Use Cases Admin-only | 10 (UC11-13, UC23-25, UC31-33) |
| Design Patterns áp dụng | 6 (Observer, MVC, DTO, Repository, 3-Layer, ECB) |
| SOLID Principles | 5 (SRP, OCP, LSP, ISP, DIP) |

---

## 🎯 MỤC ĐÍCH SỬ DỤNG

✅ **Báo cáo môn TKPM (Thiết Kế Phần Mềm)**
✅ **Tài liệu phân tích yêu cầu hệ thống**
✅ **Hướng dẫn implement cho developers**
✅ **Tài liệu testing cho QA team**
✅ **Present cho stakeholders**

---

## 📞 HỖ TRỢ

Nếu cần chi tiết hơn về bất kỳ use case nào, vui lòng xem:
- **File markdown**: `USE_CASE_SPECIFICATIONS.md` (chi tiết đầy đủ)
- **Design Patterns**: `DESIGN_PATTERNS.md` (giải thích patterns)
- **Source Code**: `src/main/java/com/applestore/` (implementation)

---

**📚 AppleStore Backend - Use Case Specifications**
**🎓 Đồ án môn TKPM - Semester 6**
**✅ 36/36 Use Cases Complete**
**🗓️ Generated: November 2025**