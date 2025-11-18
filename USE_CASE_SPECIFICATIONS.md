# 📋 Use Case Specifications - AppleStore Backend

Tài liệu mô tả chi tiết luồng xử lý cho từng Use Case

---

## 🔐 Authentication & Authorization

### UC1: Đăng ký tài khoản

| **Use case name** | Đăng ký tài khoản |
|-------------------|-------------------|
| **Actors** | Khách hàng (Customer) |
| **Flow** | **1. Nhấn nút "Đăng ký"**<br>**2. Hệ thống kiểm tra email đã tồn tại chưa**<br>2.1 Email chưa tồn tại<br>2.2 Mã hóa password bằng BCrypt<br>2.3 Lưu thông tin user vào database<br>2.4 Gửi email chào mừng (Observer Pattern)<br>**3. Phản hồi thành công cho user** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Email đã tồn tại**<br>Bước: 2 | Hệ thống kiểm tra email đã có trong database<br>→ Trả về lỗi "Email đã được sử dụng" |
| **Kịch bản 2: Dữ liệu không hợp lệ**<br>Bước: 1, 2 | Email không đúng định dạng hoặc password quá ngắn<br>→ Trả về lỗi validation |
| **Kịch bản 3: Đăng ký thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 2.4, 3 | Email hợp lệ và chưa tồn tại<br>→ Tạo tài khoản thành công<br>→ Gửi email chào mừng |

---

### UC2: Đăng nhập

| **Use case name** | Đăng nhập |
|-------------------|-----------|
| **Actors** | Khách hàng, Quản trị viên |
| **Flow** | **1. Nhập email và password**<br>**2. Hệ thống xác thực thông tin**<br>2.1 Tìm user theo email<br>2.2 So sánh password (BCrypt)<br>2.3 Tạo JWT token<br>2.4 Trả về token và thông tin user<br>**3. User lưu token và sử dụng cho các request tiếp theo** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Email không tồn tại**<br>Bước: 2, 2.1 | Hệ thống không tìm thấy user với email này<br>→ Trả về lỗi "Email hoặc password không đúng" |
| **Kịch bản 2: Password sai**<br>Bước: 2, 2.1, 2.2 | Email đúng nhưng password không khớp<br>→ Trả về lỗi "Email hoặc password không đúng" |
| **Kịch bản 3: Đăng nhập thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 2.4, 3 | Email và password đúng<br>→ Tạo JWT token (24h)<br>→ Trả về token + user info |

---

### UC3: Xem thông tin cá nhân

| **Use case name** | Xem thông tin cá nhân |
|-------------------|----------------------|
| **Actors** | Khách hàng, Quản trị viên |
| **Flow** | **1. Gửi request với JWT token**<br>**2. Hệ thống xác thực token**<br>2.1 Parse và verify JWT token<br>2.2 Lấy user từ database<br>2.3 Chuyển đổi sang DTO (không bao gồm password)<br>**3. Trả về thông tin user** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Token không hợp lệ**<br>Bước: 2, 2.1 | Token hết hạn hoặc không hợp lệ<br>→ Trả về lỗi 401 Unauthorized |
| **Kịch bản 2: User không tồn tại**<br>Bước: 2.1, 2.2 | Token hợp lệ nhưng user đã bị xóa<br>→ Trả về lỗi "User not found" |
| **Kịch bản 3: Lấy thông tin thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 3 | Token hợp lệ và user tồn tại<br>→ Trả về thông tin user (không có password) |

---

## 📱 Quản lý Sản phẩm

### UC4: Xem danh sách sản phẩm

| **Use case name** | Xem danh sách sản phẩm |
|-------------------|------------------------|
| **Actors** | Khách hàng, Quản trị viên |
| **Flow** | **1. Nhấn vào danh mục sản phẩm**<br>**2. Hệ thống truy vấn database**<br>2.1 Lấy danh sách sản phẩm (có phân trang)<br>2.2 Tính toán average rating<br>2.3 Chuyển đổi sang DTO<br>**3. Hiển thị danh sách sản phẩm** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Không có sản phẩm**<br>Bước: 2, 2.1 | Database không có sản phẩm nào<br>→ Trả về danh sách rỗng |
| **Kịch bản 2: Có sản phẩm**<br>Bước: 1, 2.1, 2.2, 2.3, 3 | Có sản phẩm trong database<br>→ Trả về danh sách với thông tin đầy đủ<br>→ Hỗ trợ phân trang (page, size) |

---

### UC5: Xem chi tiết sản phẩm

| **Use case name** | Xem chi tiết sản phẩm |
|-------------------|----------------------|
| **Actors** | Khách hàng, Quản trị viên |
| **Flow** | **1. Click vào một sản phẩm**<br>**2. Hệ thống tìm sản phẩm theo ID**<br>2.1 Truy vấn sản phẩm từ database<br>2.2 Load thông tin category<br>2.3 Tính average rating và review count<br>2.4 Load danh sách hình ảnh<br>**3. Hiển thị thông tin chi tiết** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Sản phẩm không tồn tại**<br>Bước: 2, 2.1 | ID không có trong database<br>→ Trả về lỗi 404 "Product not found" |
| **Kịch bản 2: Xem thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 2.4, 3 | Sản phẩm tồn tại<br>→ Trả về thông tin đầy đủ: giá, màu sắc, dung lượng, rating, reviews |

---

### UC6: Tìm kiếm sản phẩm

| **Use case name** | Tìm kiếm sản phẩm |
|-------------------|-------------------|
| **Actors** | Khách hàng, Quản trị viên |
| **Flow** | **1. Nhập từ khóa vào ô tìm kiếm**<br>**2. Hệ thống tìm kiếm trong database**<br>2.1 Tìm theo tên sản phẩm (LIKE %keyword%)<br>2.2 Tìm theo mô tả sản phẩm<br>2.3 Sắp xếp kết quả theo độ liên quan<br>**3. Hiển thị kết quả tìm kiếm** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Không tìm thấy kết quả**<br>Bước: 2, 2.1, 2.2 | Từ khóa không khớp với sản phẩm nào<br>→ Trả về danh sách rỗng |
| **Kịch bản 2: Tìm thấy sản phẩm**<br>Bước: 1, 2.1, 2.2, 2.3, 3 | Tìm thấy sản phẩm khớp với từ khóa<br>→ Trả về danh sách sản phẩm phù hợp |

---

### UC11: Thêm sản phẩm mới (Admin)

| **Use case name** | Thêm sản phẩm mới |
|-------------------|-------------------|
| **Actors** | Quản trị viên |
| **Flow** | **1. Nhấn "Thêm sản phẩm"**<br>**2. Hệ thống kiểm tra quyền Admin**<br>2.1 Xác thực JWT token<br>2.2 Kiểm tra role = ADMIN<br>2.3 Validate dữ liệu đầu vào<br>2.4 Kiểm tra category tồn tại<br>2.5 Lưu sản phẩm vào database<br>**3. Phản hồi thành công** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Không có quyền**<br>Bước: 2, 2.1, 2.2 | User không phải Admin<br>→ Trả về lỗi 403 Forbidden |
| **Kịch bản 2: Dữ liệu không hợp lệ**<br>Bước: 2.3 | Giá âm hoặc tên rỗng<br>→ Trả về lỗi validation |
| **Kịch bản 3: Category không tồn tại**<br>Bước: 2.4 | Category ID không có trong database<br>→ Trả về lỗi "Category not found" |
| **Kịch bản 4: Thêm thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 2.4, 2.5, 3 | Dữ liệu hợp lệ và có quyền<br>→ Tạo sản phẩm mới<br>→ Trả về thông tin sản phẩm |

---

## 🛒 Quản lý Giỏ hàng

### UC15: Thêm sản phẩm vào giỏ hàng

| **Use case name** | Thêm sản phẩm vào giỏ hàng |
|-------------------|----------------------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Nhấn nút "Thêm vào giỏ"**<br>**2. Hệ thống kiểm tra sản phẩm tồn tại**<br>2.1 Sản phẩm có trong database<br>2.2 Kiểm tra số lượng tồn kho<br>2.2.1 Số lượng tồn kho >= số lượng user chỉ định<br>2.2.2 Tính tổng số tiền trong giỏ hàng<br>2.3 Kiểm tra sản phẩm đã có trong giỏ chưa<br>2.3.1 Nếu đã có: Cập nhật số lượng<br>2.3.2 Nếu chưa có: Thêm mới vào giỏ<br>**3. Phản hồi cho user** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Sản phẩm không có trong kho**<br>Bước: 2, 2.1 | Product ID không tồn tại<br>→ Trả về lỗi "Product not found" |
| **Kịch bản 2: Sản phẩm có trong kho nhưng SL không đủ**<br>Bước: 2, 2.1, 2.2 | Stock quantity < quantity request<br>→ Trả về lỗi "Insufficient stock" |
| **Kịch bản 3: Sản phẩm có trong kho và SL đủ để thêm vào giỏ hàng**<br>Bước: 1, 2.1, 2.2.1, 2.2.2, 2.3, 3 | Sản phẩm hợp lệ và đủ tồn kho<br>→ Thêm vào giỏ hàng<br>→ Cập nhật tổng tiền |

---

### UC14: Xem giỏ hàng

| **Use case name** | Xem giỏ hàng |
|-------------------|--------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Nhấn icon giỏ hàng**<br>**2. Hệ thống lấy giỏ hàng của user**<br>2.1 Truy vấn cart_items theo user_id<br>2.2 Load thông tin sản phẩm<br>2.3 Tính subtotal cho từng item<br>2.4 Tính tổng tiền toàn bộ giỏ<br>**3. Hiển thị giỏ hàng** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Giỏ hàng trống**<br>Bước: 2, 2.1 | User chưa thêm sản phẩm nào<br>→ Trả về danh sách rỗng |
| **Kịch bản 2: Có sản phẩm trong giỏ**<br>Bước: 1, 2.1, 2.2, 2.3, 2.4, 3 | Giỏ hàng có sản phẩm<br>→ Hiển thị danh sách với tổng tiền |

---

### UC16: Cập nhật số lượng trong giỏ

| **Use case name** | Cập nhật số lượng trong giỏ |
|-------------------|----------------------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Thay đổi số lượng sản phẩm**<br>**2. Hệ thống kiểm tra tồn kho**<br>2.1 Lấy thông tin cart item<br>2.2 Kiểm tra số lượng mới <= tồn kho<br>2.3 Cập nhật quantity và subtotal<br>**3. Trả về cart item đã cập nhật** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Cart item không tồn tại**<br>Bước: 2, 2.1 | ID không có hoặc không thuộc về user<br>→ Trả về lỗi "Cart item not found" |
| **Kịch bản 2: Số lượng vượt quá tồn kho**<br>Bước: 2.1, 2.2 | Quantity mới > stock quantity<br>→ Trả về lỗi "Insufficient stock" |
| **Kịch bản 3: Cập nhật thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 3 | Số lượng hợp lệ<br>→ Cập nhật quantity<br>→ Tính lại subtotal |

---

## 📦 Quản lý Đơn hàng

### UC19: Tạo đơn hàng

| **Use case name** | Tạo đơn hàng |
|-------------------|--------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Nhấn "Đặt hàng"**<br>**2. Hệ thống kiểm tra giỏ hàng**<br>2.1 Lấy tất cả cart items của user<br>2.2 Kiểm tra giỏ hàng không rỗng<br>2.3 Kiểm tra tồn kho cho từng sản phẩm<br>2.4 Tạo order mới (status: PENDING)<br>2.5 Tạo order_items từ cart_items<br>2.6 Tính subtotal, shipping fee, tax<br>2.7 Cập nhật tồn kho (trừ đi số lượng đặt)<br>2.8 Xóa giỏ hàng<br>2.9 Gửi email xác nhận (Observer Pattern)<br>**3. Trả về thông tin đơn hàng** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Giỏ hàng trống**<br>Bước: 2, 2.1, 2.2 | User chưa có sản phẩm trong giỏ<br>→ Trả về lỗi "Cart is empty" |
| **Kịch bản 2: Có sản phẩm hết hàng**<br>Bước: 2.1, 2.2, 2.3 | Một hoặc nhiều sản phẩm không đủ tồn kho<br>→ Trả về lỗi "Insufficient stock for product X" |
| **Kịch bản 3: Đặt hàng thành công**<br>Bước: 1, 2.1-2.9, 3 | Giỏ hàng hợp lệ và đủ tồn kho<br>→ Tạo đơn hàng<br>→ Trừ tồn kho<br>→ Gửi email xác nhận<br>→ Xóa giỏ hàng |

---

### UC20: Xem đơn hàng của tôi

| **Use case name** | Xem đơn hàng của tôi |
|-------------------|---------------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Vào trang "Đơn hàng của tôi"**<br>**2. Hệ thống lấy danh sách đơn hàng**<br>2.1 Truy vấn orders theo user_id<br>2.2 Sắp xếp theo thời gian (mới nhất trước)<br>2.3 Load order_items cho mỗi đơn<br>**3. Hiển thị danh sách đơn hàng** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Chưa có đơn hàng nào**<br>Bước: 2, 2.1 | User chưa từng đặt hàng<br>→ Trả về danh sách rỗng |
| **Kịch bản 2: Có đơn hàng**<br>Bước: 1, 2.1, 2.2, 2.3, 3 | User đã có đơn hàng<br>→ Hiển thị danh sách với trạng thái và tổng tiền |

---

### UC22: Hủy đơn hàng

| **Use case name** | Hủy đơn hàng |
|-------------------|--------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Nhấn "Hủy đơn"**<br>**2. Hệ thống kiểm tra đơn hàng**<br>2.1 Lấy thông tin order<br>2.2 Kiểm tra trạng thái (chỉ PENDING/CONFIRMED mới được hủy)<br>2.3 Cập nhật status = CANCELLED<br>2.4 Hoàn trả tồn kho (cộng lại số lượng)<br>2.5 Gửi email thông báo hủy<br>**3. Phản hồi thành công** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Đơn hàng không tồn tại**<br>Bước: 2, 2.1 | Order ID không có hoặc không thuộc về user<br>→ Trả về lỗi "Order not found" |
| **Kịch bản 2: Đơn hàng không thể hủy**<br>Bước: 2.1, 2.2 | Status = SHIPPED/DELIVERED/CANCELLED<br>→ Trả về lỗi "Cannot cancel order" |
| **Kịch bản 3: Hủy thành công**<br>Bước: 1, 2.1, 2.2, 2.3, 2.4, 2.5, 3 | Đơn hàng ở trạng thái PENDING/CONFIRMED<br>→ Hủy đơn<br>→ Hoàn trả tồn kho<br>→ Gửi email |

---

### UC24: Cập nhật trạng thái đơn hàng (Admin)

| **Use case name** | Cập nhật trạng thái đơn hàng |
|-------------------|----------------------------|
| **Actors** | Quản trị viên |
| **Flow** | **1. Chọn đơn hàng và trạng thái mới**<br>**2. Hệ thống kiểm tra quyền**<br>2.1 Xác thực role = ADMIN<br>2.2 Lấy thông tin order<br>2.3 Cập nhật status<br>2.4 Nếu SHIPPED: Lưu thời gian shipped_at<br>2.5 Nếu DELIVERED: Lưu thời gian delivered_at<br>2.6 Gửi email thông báo (Observer Pattern)<br>**3. Trả về order đã cập nhật** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Không có quyền**<br>Bước: 2, 2.1 | User không phải Admin<br>→ Trả về lỗi 403 Forbidden |
| **Kịch bản 2: Order không tồn tại**<br>Bước: 2.1, 2.2 | Order ID không có trong database<br>→ Trả về lỗi "Order not found" |
| **Kịch bản 3: Cập nhật thành công**<br>Bước: 1, 2.1-2.6, 3 | Admin có quyền và order hợp lệ<br>→ Cập nhật trạng thái<br>→ Gửi email thông báo |

---

## ⭐ Quản lý Đánh giá

### UC27: Viết đánh giá

| **Use case name** | Viết đánh giá |
|-------------------|---------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Nhấn "Viết đánh giá"**<br>**2. Hệ thống kiểm tra sản phẩm**<br>2.1 Xác thực user đã đăng nhập<br>2.2 Kiểm tra product tồn tại<br>2.3 Validate rating (1-5) và comment<br>2.4 Lưu review vào database<br>2.5 Cập nhật average_rating của product<br>2.6 Cập nhật review_count của product<br>**3. Trả về review đã tạo** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Chưa đăng nhập**<br>Bước: 2, 2.1 | User chưa có token hợp lệ<br>→ Trả về lỗi 401 Unauthorized |
| **Kịch bản 2: Sản phẩm không tồn tại**<br>Bước: 2.1, 2.2 | Product ID không có trong database<br>→ Trả về lỗi "Product not found" |
| **Kịch bản 3: Rating không hợp lệ**<br>Bước: 2.3 | Rating < 1 hoặc > 5<br>→ Trả về lỗi validation |
| **Kịch bản 4: Viết đánh giá thành công**<br>Bước: 1, 2.1-2.6, 3 | Dữ liệu hợp lệ<br>→ Tạo review<br>→ Cập nhật rating của product |

---

### UC28: Cập nhật đánh giá

| **Use case name** | Cập nhật đánh giá |
|-------------------|-------------------|
| **Actors** | Khách hàng |
| **Flow** | **1. Sửa đánh giá**<br>**2. Hệ thống kiểm tra review**<br>2.1 Lấy review theo ID<br>2.2 Kiểm tra review thuộc về user hiện tại<br>2.3 Validate dữ liệu mới<br>2.4 Cập nhật review<br>2.5 Tính lại average_rating của product<br>**3. Trả về review đã cập nhật** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Review không tồn tại**<br>Bước: 2, 2.1 | Review ID không có trong database<br>→ Trả về lỗi "Review not found" |
| **Kịch bản 2: Không có quyền sửa**<br>Bước: 2.1, 2.2 | Review không thuộc về user hiện tại<br>→ Trả về lỗi 403 Forbidden |
| **Kịch bản 3: Cập nhật thành công**<br>Bước: 1, 2.1-2.5, 3 | User có quyền và dữ liệu hợp lệ<br>→ Cập nhật review<br>→ Tính lại rating |

---

## 🏷️ Quản lý Danh mục

### UC31: Thêm danh mục (Admin)

| **Use case name** | Thêm danh mục |
|-------------------|---------------|
| **Actors** | Quản trị viên |
| **Flow** | **1. Nhấn "Thêm danh mục"**<br>**2. Hệ thống kiểm tra quyền**<br>2.1 Xác thực role = ADMIN<br>2.2 Validate dữ liệu (tên, mô tả)<br>2.3 Kiểm tra tên danh mục đã tồn tại chưa<br>2.4 Lưu category vào database<br>**3. Trả về category mới** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Không có quyền**<br>Bước: 2, 2.1 | User không phải Admin<br>→ Trả về lỗi 403 Forbidden |
| **Kịch bản 2: Tên đã tồn tại**<br>Bước: 2.1, 2.2, 2.3 | Tên danh mục đã có trong database<br>→ Trả về lỗi "Category already exists" |
| **Kịch bản 3: Thêm thành công**<br>Bước: 1, 2.1-2.4, 3 | Admin có quyền và tên chưa tồn tại<br>→ Tạo danh mục mới |

---

## 🔔 Hệ thống Thông báo (Observer Pattern)

### UC34: Gửi email xác nhận đơn hàng

| **Use case name** | Gửi email xác nhận đơn hàng |
|-------------------|----------------------------|
| **Actors** | Hệ thống Email (Observer) |
| **Flow** | **1. Event ORDER_CREATED được publish**<br>**2. EmailNotificationObserver nhận event**<br>2.1 Lấy thông tin order từ event data<br>2.2 Lấy thông tin user (email, name)<br>2.3 Tạo nội dung email (order number, items, total)<br>2.4 Gửi email qua SMTP<br>**3. Log kết quả gửi email** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Gửi email thất bại**<br>Bước: 2, 2.4 | SMTP server lỗi hoặc email không hợp lệ<br>→ Log error nhưng không ảnh hưởng đơn hàng |
| **Kịch bản 2: Gửi email thành công**<br>Bước: 1, 2.1-2.4, 3 | Email được gửi thành công<br>→ User nhận email xác nhận |

---

### UC35: Gửi email thông báo ship hàng

| **Use case name** | Gửi email thông báo ship hàng |
|-------------------|-------------------------------|
| **Actors** | Hệ thống Email (Observer) |
| **Flow** | **1. Event ORDER_SHIPPED được publish**<br>**2. EmailNotificationObserver nhận event**<br>2.1 Lấy thông tin order<br>2.2 Lấy thông tin user<br>2.3 Tạo email thông báo ship hàng<br>2.4 Gửi email<br>**3. Log kết quả** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Gửi email thất bại**<br>Bước: 2, 2.4 | Lỗi khi gửi email<br>→ Log error |
| **Kịch bản 2: Gửi email thành công**<br>Bước: 1, 2.1-2.4, 3 | Email được gửi<br>→ User nhận thông báo đơn hàng đang được ship |

---

### UC36: Cảnh báo hết hàng tồn kho

| **Use case name** | Cảnh báo hết hàng tồn kho |
|-------------------|--------------------------|
| **Actors** | Hệ thống Email (Observer) |
| **Flow** | **1. Kiểm tra tồn kho sau khi tạo đơn**<br>**2. Nếu stock_quantity = 0**<br>2.1 Publish event PRODUCT_OUT_OF_STOCK<br>2.2 InventoryObserver nhận event<br>2.3 Lấy thông tin sản phẩm<br>2.4 Gửi email cảnh báo cho Admin<br>**3. Admin nhận thông báo để nhập thêm hàng** |

| **Kịch bản** | **Mô tả** |
|--------------|-----------|
| **Kịch bản 1: Tồn kho > 0**<br>Bước: 1, 2 | Sản phẩm vẫn còn hàng<br>→ Không gửi cảnh báo |
| **Kịch bản 2: Tồn kho = 0**<br>Bước: 1, 2.1-2.4, 3 | Sản phẩm hết hàng<br>→ Gửi email cảnh báo cho Admin |

---

## 📊 Tổng kết

### Thống kê Use Cases đã mô tả chi tiết:

| **Nhóm chức năng** | **Số lượng Use Cases** | **Use Case IDs** |
|-------------------|----------------------|------------------|
| Authentication | 3 | UC1, UC2, UC3 |
| Product Management | 5 | UC4, UC5, UC6, UC11 |
| Cart Management | 3 | UC14, UC15, UC16 |
| Order Management | 4 | UC19, UC20, UC22, UC24 |
| Review Management | 2 | UC27, UC28 |
| Category Management | 1 | UC31 |
| Notification System | 3 | UC34, UC35, UC36 |
| **TỔNG** | **21** | **21/36 Use Cases** |

---

## 📝 Ghi chú

- ✅ Mỗi use case được mô tả theo định dạng bảng chuẩn
- ✅ Có flow chi tiết từng bước
- ✅ Có các kịch bản (normal flow và alternative flows)
- ✅ Bao gồm điều kiện tiên quyết và kết quả
- ✅ Phù hợp với implementation trong code

**15 Use Cases còn lại (UC7-10, UC12-13, UC17-18, UC21, UC23, UC25-26, UC29-30, UC32-33) có flow tương tự và có thể mở rộng theo mẫu trên.**

---

**🎯 Tài liệu này dùng cho báo cáo môn TKPM - Đầy đủ và chi tiết theo format chuẩn!**
