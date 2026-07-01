## 1. Prompt tối ưu (theo cấu trúc 5 thành phần)

```text
# Vai trò (Role)

Bạn là một Senior Business Analyst (BA) kết hợp Software Architect với hơn 15 năm kinh nghiệm xây dựng tài liệu Software Requirements Specification (SRS). Bạn có chuyên môn sâu về Authentication, Authorization, JWT, OAuth2, RBAC, API Security và phân tích Edge Cases. Mục tiêu của bạn là tạo tài liệu Functional Requirements có thể được sử dụng trực tiếp cho nhóm Backend, Frontend, QA và DevOps.

# Mục tiêu (Objective)

Hãy xây dựng tài liệu Functional Requirements cho chức năng "Đăng nhập (Login)" của hệ thống Shop AI.

Tài liệu phải mô tả đầy đủ:
- Mục tiêu chức năng.
- Actors.
- Preconditions.
- Postconditions.
- Main Flow.
- Alternative Flow.
- Exception Flow.
- Business Rules.
- Validation Rules.
- Security Requirements.
- Acceptance Criteria.

Đặc tả phải đủ chi tiết để lập trình viên có thể triển khai API và QA có thể viết Test Case mà không cần hỏi thêm.

# Ngữ cảnh (Context)

Hệ thống Shop AI sử dụng:

- JWT Authentication
- RBAC (Role-Based Access Control)
- RESTful API
- Access Token + Refresh Token
- Password được mã hóa bằng BCrypt
- Người dùng đăng nhập bằng Email và Password
- Sau khi đăng nhập thành công, hệ thống sinh Access Token và Refresh Token
- Mỗi Access Token chứa UserId, Role và Expiration Time
- Refresh Token được lưu trong Database
- Các Role gồm:
  - ADMIN
  - STAFF
  - CUSTOMER

Sau khi xác thực thành công:
- Security Context được thiết lập.
- Người dùng được phân quyền theo RBAC.
- Chỉ tài khoản Active mới được phép đăng nhập.

# Ràng buộc (Constraints)

Đặc tả phải xử lý đầy đủ các trường hợp bình thường và các Edge Cases.

BẮT BUỘC mô tả rõ cách hệ thống xử lý các tình huống sau:

1. Người dùng nhập sai mật khẩu quá 5 lần liên tiếp.
   - Có khóa tài khoản hay không.
   - Thời gian khóa.
   - Thông báo trả về.
   - Ghi Audit Log.
   - HTTP Status Code.
   - Error Code.

2. Access Token JWT hết hạn trong lúc người dùng đang thao tác.
   - API phản hồi gì.
   - Có sử dụng Refresh Token hay không.
   - Điều kiện cấp Access Token mới.
   - Khi nào yêu cầu đăng nhập lại.
   - HTTP Status Code.
   - Error Code.

3. Người dùng cố đăng nhập khi tài khoản ở trạng thái Inactive.
   - Điều kiện kiểm tra.
   - Thông báo trả về.
   - Không sinh JWT.
   - Không tạo Session.
   - Audit Log.
   - HTTP Status Code.
   - Error Code.

Ngoài ra phải mô tả thêm:

- Sai Email
- Sai Password
- Thiếu Email
- Thiếu Password
- Email sai định dạng
- Refresh Token không hợp lệ
- Refresh Token bị thu hồi
- JWT giả mạo
- JWT sai chữ ký
- JWT hết hạn
- Token blacklist
- Người dùng logout
- Đồng thời đăng nhập trên nhiều thiết bị
- Rate Limiting
- Audit Logging
- Password Policy
- Security Best Practices

Mỗi Functional Requirement phải có:
- Requirement ID
- Requirement Name
- Priority
- Description
- Preconditions
- Main Flow
- Alternative Flow
- Exception Flow
- Business Rules
- Validation Rules
- Input
- Output
- API Response
- Acceptance Criteria

# Định dạng (Output Format)

Xuất kết quả dưới dạng Markdown.

Cấu trúc tài liệu gồm:

1. Module Overview
2. Actors
3. Functional Requirements
4. Business Rules
5. Validation Rules
6. Security Requirements
7. Exception Handling
8. API Response Examples
9. Acceptance Criteria

Sử dụng bảng Markdown cho Functional Requirements.

Mỗi Requirement phải có ID dạng:
FR-AUTH-001
FR-AUTH-002
...

Không bỏ sót bất kỳ Edge Case nào.
```

---

## 2. Functional Requirements do AI sinh ra

````text
# Functional Requirements - Authentication Module (Shop AI)

# 1. Module Overview

Module Authentication cho phép người dùng xác thực bằng Email và Password, sử dụng JWT để xác thực phiên làm việc và RBAC để phân quyền truy cập tài nguyên.

---

# 2. Actors

| Actor    |   Description  |
|----------|----------------|
| CUSTOMER | Người mua hàng |
| STAFF    | Nhân viên      |
| ADMIN    | Quản trị hệ thống |

---

# 3. Functional Requirements

| ID       | FR-AUTH-001 |
|----------|-------------|
| Name     | User Login  |
| Priority | High        |

### Description

Hệ thống cho phép người dùng đăng nhập bằng Email và Password hợp lệ.

### Preconditions

- Tài khoản tồn tại.
- Tài khoản ở trạng thái Active.
- Password đúng.

### Main Flow

1. Người dùng nhập Email và Password.
2. Hệ thống kiểm tra định dạng Email.
3. Kiểm tra Password.
4. Xác thực BCrypt.
5. Kiểm tra trạng thái tài khoản.
6. Sinh Access Token.
7. Sinh Refresh Token.
8. Lưu Refresh Token.
9. Thiết lập Security Context.
10. Trả JWT và thông tin Role.

### Postconditions

- Người dùng đăng nhập thành công.
- JWT được cấp.
- Refresh Token được lưu.

---

# FR-AUTH-002

## Exception Flow

### Sai Email

Response

HTTP 401 Unauthorized

ErrorCode

AUTH_USER_NOT_FOUND

Message

Email hoặc mật khẩu không chính xác.

---

### Sai Password

HTTP 401 Unauthorized

AUTH_INVALID_PASSWORD

Tăng bộ đếm số lần đăng nhập thất bại.

---

### Nhập sai Password quá 5 lần

Business Rule

- Sau 5 lần thất bại liên tiếp:
  - Khóa tài khoản tạm thời 30 phút.
  - Không cho phép đăng nhập.
  - Ghi Audit Log.
  - Gửi cảnh báo bảo mật (nếu có).

Response

HTTP 423 Locked

ErrorCode

AUTH_ACCOUNT_TEMP_LOCKED

Message

Tài khoản đã bị khóa tạm thời do đăng nhập sai quá nhiều lần.

---

### Tài khoản Inactive

Business Rule

- Không xác thực Password tiếp tục.
- Không sinh JWT.
- Không sinh Refresh Token.
- Không tạo Security Context.
- Ghi Audit Log.

Response

HTTP 403 Forbidden

ErrorCode

AUTH_ACCOUNT_INACTIVE

Message

Tài khoản đã bị khóa hoặc chưa được kích hoạt.

---

### JWT hết hạn trong phiên làm việc

Business Rule

- API trả về Access Token Expired.
- Kiểm tra Refresh Token.
- Nếu Refresh Token hợp lệ:
  - Cấp Access Token mới.
- Nếu Refresh Token hết hạn hoặc bị thu hồi:
  - Yêu cầu đăng nhập lại.

Response

HTTP 401 Unauthorized

ErrorCode

AUTH_ACCESS_TOKEN_EXPIRED

Message

Access Token đã hết hạn.

---

### JWT sai chữ ký

HTTP 401 Unauthorized

AUTH_INVALID_SIGNATURE

Không cho phép truy cập.

---

### JWT giả mạo

HTTP 401 Unauthorized

AUTH_INVALID_TOKEN

Audit Log được ghi.

---

### Refresh Token không hợp lệ

HTTP 401 Unauthorized

AUTH_INVALID_REFRESH_TOKEN

Yêu cầu đăng nhập lại.

---

### Refresh Token bị thu hồi

HTTP 401 Unauthorized

AUTH_REFRESH_TOKEN_REVOKED

Không cấp Access Token mới.

---

### Token Blacklist

Nếu Access Token nằm trong Blacklist:

HTTP 401 Unauthorized

AUTH_TOKEN_REVOKED

---

# 4. Business Rules

BR-001

Chỉ tài khoản Active mới được đăng nhập.

BR-002

Password được mã hóa BCrypt.

BR-003

JWT phải chứa:

- UserId
- Email
- Role
- Expiration Time

BR-004

Role được phân quyền bằng RBAC.

BR-005

Sau Logout:

- Refresh Token bị thu hồi.
- Access Token được thêm vào Blacklist.

BR-006

Cho phép đăng nhập trên nhiều thiết bị nếu chính sách hệ thống cho phép.

BR-007

Rate Limiting:
- Tối đa 10 request/phút/IP.
- Vượt quá giới hạn trả về HTTP 429 Too Many Requests.

---

# 5. Validation Rules

- Email không được để trống.
- Password không được để trống.
- Email đúng định dạng RFC.
- Password tối thiểu 8 ký tự.
- Password tối đa 128 ký tự.

---

# 6. Security Requirements

- BCrypt Password Hashing.
- JWT Signed.
- HTTPS bắt buộc.
- Audit Logging.
- Token Revocation.
- Refresh Token Rotation.
- RBAC Authorization.
- Không trả về nguyên nhân chi tiết khi đăng nhập thất bại.
- Không ghi Password vào Log.

---

# 7. API Response Examples

## Success

HTTP 200 OK

```json
{
  "accessToken": "...",
  "refreshToken": "...",
  "tokenType": "Bearer",
  "role": "CUSTOMER"
}
```

## Invalid Password

HTTP 401

```json
{
  "errorCode": "AUTH_INVALID_PASSWORD",
  "message": "Email hoặc mật khẩu không chính xác."
}
```

## Account Locked

HTTP 423

```json
{
  "errorCode": "AUTH_ACCOUNT_TEMP_LOCKED",
  "message": "Tài khoản đã bị khóa tạm thời."
}
```

## Inactive Account

HTTP 403

```json
{
  "errorCode": "AUTH_ACCOUNT_INACTIVE",
  "message": "Tài khoản chưa được kích hoạt hoặc đã bị khóa."
}
```

## Token Expired

HTTP 401

```json
{
  "errorCode": "AUTH_ACCESS_TOKEN_EXPIRED",
  "message": "Access Token đã hết hạn."
}
```

---

# 8. Acceptance Criteria

- Người dùng Active đăng nhập thành công nhận JWT hợp lệ.
- Người dùng nhập sai Password quá 5 lần bị khóa tạm thời và ghi Audit Log.
- Người dùng Inactive không được cấp JWT.
- JWT hết hạn được xử lý thông qua Refresh Token nếu còn hợp lệ.
- Refresh Token bị thu hồi yêu cầu người dùng đăng nhập lại.
- Mọi thao tác xác thực đều được ghi Audit Log.
- Phân quyền RBAC được áp dụng chính xác theo Role.
````