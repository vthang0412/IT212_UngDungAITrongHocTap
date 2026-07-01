## 1. Prompt thiết kế cho Antigravity

```text
# Vai trò (Role)

Bạn là một Senior Solution Architect kiêm System Analyst với hơn 15 năm kinh nghiệm xây dựng hệ thống thương mại điện tử quy mô lớn. Bạn có chuyên môn về Software Architecture, Performance Engineering, Database Optimization, Security Engineering và viết tài liệu Software Requirements Specification (SRS) theo chuẩn IEEE 830/ISO 29148.

Hệ thống Antigravity đã index toàn bộ mã nguồn của dự án Guai-api, bao gồm các module Authentication, Product, Cart, Order và cấu hình MySQL.

# Mục tiêu (Objective)

Xây dựng tài liệu Non-Functional Requirements (NFR) cho hệ thống Guai-api nhằm đảm bảo hệ thống đáp ứng các yêu cầu về hiệu năng, khả năng mở rộng và bảo mật khi phục vụ lượng truy cập lớn.

# Ngữ cảnh (Context)

Guai-api là hệ thống RESTful API sử dụng:

- Spring Boot
- MySQL
- JWT Authentication
- RBAC Authorization
- REST API
- BCrypt Password Encoding

Hệ thống cần sẵn sàng phục vụ các đợt khuyến mãi và Flash Sale với lưu lượng truy cập tăng đột biến.

# Ràng buộc (Constraints)

Bắt buộc phân tích và đưa ra các chỉ tiêu kỹ thuật cụ thể cho các nhóm yêu cầu sau:

## 1. Response Time

Đặc tả yêu cầu hiệu năng cho các endpoint tra cứu sản phẩm:

- GET /products
- GET /products/{id}
- GET /products/search

Mỗi yêu cầu phải nêu rõ:

- Thời gian phản hồi trung bình (Average Response Time)
- Thời gian phản hồi tối đa (Maximum Response Time)
- Mức tải đồng thời (Concurrent Users)
- Throughput (Requests/Second)
- Điều kiện đo kiểm

## 2. MySQL Performance

Đề xuất chiến lược tối ưu cơ sở dữ liệu:

- Các cột cần tạo Index
- Composite Index phù hợp
- Tránh Full Table Scan
- Tối ưu JOIN
- Phân tích truy vấn bằng EXPLAIN
- Quy tắc đánh Index cho Product, Category, User, Cart và Order
- Khuyến nghị về Connection Pool

## 3. JWT Performance & Security

Đặc tả yêu cầu cho luồng xác thực JWT:

- Thời gian sinh Access Token
- Thời gian xác thực JWT
- Độ trễ tối đa cho mỗi request có JWT
- Thời gian Refresh Token
- Thuật toán ký (HS256 hoặc RS256)
- Thời gian hết hạn Access Token
- Chính sách Refresh Token
- Token Revocation
- Token Blacklist
- HTTPS bắt buộc
- Chống Replay Attack
- Audit Logging
- Rate Limiting

Mỗi yêu cầu phải có:

- Requirement ID
- Category
- Requirement
- Technical Target
- Measurement Method
- Priority

# Định dạng (Output Format)

Xuất kết quả dưới dạng Markdown Table.

Các Requirement ID theo định dạng:

NFR-001
NFR-002
NFR-003
...

Sau bảng, bổ sung phần "Technical Recommendations" tóm tắt các khuyến nghị tối ưu hệ thống.

Không đưa ra mô tả chung chung; mọi NFR phải có chỉ tiêu định lượng rõ ràng để nhóm Dev, QA và DevOps có thể kiểm thử và triển khai.
```

---

## 2. Bảng tài liệu NFR do AI sinh ra

```markdown
| Requirement ID | Category | Requirement | Technical Target | Measurement Method | Priority |
|----------------|----------|-------------|------------------|--------------------|----------|
| NFR-001 | Response Time | Endpoint GET /products phải phản hồi nhanh trong điều kiện tải thông thường. | Response Time trung bình ≤ 200 ms, tối đa ≤ 500 ms với 1.000 người dùng đồng thời. | Kiểm thử bằng JMeter/Gatling, lấy giá trị P95 và P99. | Critical |
| NFR-002 | Response Time | Endpoint GET /products/{id} phải tối ưu truy vấn theo khóa chính. | Trung bình ≤ 100 ms, tối đa ≤ 300 ms. | Đo thời gian phản hồi API trong môi trường production-like. | High |
| NFR-003 | Response Time | Endpoint GET /products/search phải hỗ trợ tìm kiếm hiệu quả. | Trung bình ≤ 300 ms, tối đa ≤ 800 ms với dữ liệu ≥ 1 triệu sản phẩm. | Benchmark với tập dữ liệu lớn và truy vấn đa điều kiện. | High |
| NFR-004 | Throughput | Hệ thống phải đáp ứng lưu lượng lớn trong các đợt Flash Sale. | Tối thiểu 2.000 request/giây cho các API tra cứu sản phẩm. | Stress Test và Load Test. | Critical |
| NFR-005 | MySQL Indexing | Các cột product_id, category_id, user_id, order_id phải được đánh Index. | 100% truy vấn theo khóa chính hoặc khóa ngoại sử dụng Index. | Phân tích bằng EXPLAIN. | Critical |
| NFR-006 | MySQL Indexing | Tạo Composite Index cho các truy vấn thường xuyên. | Ví dụ: (category_id, status), (user_id, created_at), (order_status, created_at). | Kiểm tra Execution Plan và thời gian truy vấn. | High |
| NFR-007 | MySQL Performance | Tránh Full Table Scan trên các bảng lớn. | Tỷ lệ truy vấn Full Table Scan < 5%. | Kiểm tra EXPLAIN và Slow Query Log. | High |
| NFR-008 | Database Connection | Quản lý Connection Pool hiệu quả. | HikariCP với tối đa 50 kết nối hoạt động, thời gian chờ ≤ 30 giây. | Theo dõi bằng Metrics và Monitoring. | Medium |
| NFR-009 | JWT Performance | Thời gian sinh Access Token phải thấp. | ≤ 50 ms mỗi lần cấp phát. | Benchmark tại tầng Authentication Service. | High |
| NFR-010 | JWT Validation | Thời gian xác thực Access Token. | ≤ 20 ms mỗi request. | Đo thời gian xử lý trong Security Filter. | Critical |
| NFR-011 | JWT Security | Access Token phải có thời gian sống ngắn. | Thời gian hết hạn 15 phút. | Kiểm tra cấu hình và kiểm thử bảo mật. | High |
| NFR-012 | Refresh Token | Refresh Token phải được sử dụng để cấp Access Token mới. | Thời gian sống 7 ngày; chỉ cấp Access Token mới khi Refresh Token hợp lệ. | Kiểm thử luồng Refresh Token. | High |
| NFR-013 | Token Revocation | Token bị thu hồi không được phép sử dụng lại. | 100% Access Token trong Blacklist bị từ chối. | Kiểm thử với Token Blacklist. | Critical |
| NFR-014 | Security | Mọi giao tiếp với API phải sử dụng HTTPS/TLS. | 100% lưu lượng qua HTTPS (TLS 1.2 trở lên). | Kiểm tra cấu hình máy chủ và chứng chỉ SSL/TLS. | Critical |
| NFR-015 | Security | Chống Replay Attack và giới hạn tần suất yêu cầu. | Áp dụng Rate Limiting (100 request/phút/người dùng) và xác thực JWT trên mọi request. | Kiểm thử tấn công lặp lại và vượt ngưỡng Rate Limit. | High |
| NFR-016 | Audit Logging | Ghi nhận đầy đủ các sự kiện xác thực và lỗi bảo mật. | 100% sự kiện Login, Refresh Token, Logout và Token Revocation được lưu Audit Log. | Kiểm tra nhật ký hệ thống và truy vết sự kiện. | Medium |
```

### Technical Recommendations

* Sử dụng **HikariCP** để tối ưu Connection Pool và giảm thời gian chờ kết nối MySQL.
* Đánh **Index** cho các cột thường xuyên lọc, sắp xếp và JOIN; sử dụng **Composite Index** theo đúng mẫu truy vấn để hạn chế Full Table Scan.
* Kiểm tra hiệu năng truy vấn định kỳ bằng **EXPLAIN** và **Slow Query Log**, tối ưu các câu SQL có chi phí cao.
* Áp dụng **JWT Access Token** ngắn hạn (15 phút), **Refresh Token** dài hạn (7 ngày) kết hợp **Token Rotation** và **Token Blacklist** để tăng cường bảo mật.
* Bắt buộc sử dụng **HTTPS/TLS**, triển khai **Rate Limiting**, **Audit Logging** và giám sát các chỉ số hiệu năng (Response Time, Throughput, Connection Pool, CPU, Memory) để đảm bảo hệ thống hoạt động ổn định trong các đợt lưu lượng truy cập lớn.