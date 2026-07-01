## 1. Prompt thiết kế cho Antigravity

```text
# Vai trò (Role)

Bạn là một Senior System Analyst kiêm Software Architect có hơn 15 năm kinh nghiệm phân tích hệ thống Java Spring Boot, Domain-Driven Design (DDD), SRS và Code Review. Hệ thống Antigravity đã index toàn bộ mã nguồn của dự án Guai-api, bao gồm CartService.java và các entity/repository liên quan.

# Mục tiêu (Objective)

Phân tích đoạn mã CartService hiện tại để:

1. Phát hiện các lỗ hổng logic, thiếu kiểm tra nghiệp vụ và các Edge Cases.
2. Chuyển các phát hiện đó thành danh sách Business Rules theo chuẩn Software Requirements Specification (SRS) để đội phát triển sử dụng làm cơ sở vá lỗi và cập nhật tài liệu yêu cầu.

# Ngữ cảnh (Context)

Module Cart cho phép người dùng thêm sản phẩm vào giỏ hàng.

Hiện tại CartService thực hiện các bước:
- Tìm Product theo productId.
- Nếu CartItem đã tồn tại thì cộng thêm quantity.
- Nếu chưa tồn tại thì tạo CartItem mới.
- Lưu CartItem.

Hệ thống có quản lý Inventory (tồn kho) của Product.

# Ràng buộc (Constraints)

Thực hiện đồng thời hai nhiệm vụ:

## Nhiệm vụ 1 - Phân tích lỗi logic

Phân tích và chỉ rõ:

- quantity <= 0
- quantity âm
- quantity bằng 0
- quantity vượt quá tồn kho
- Tổng số lượng sau khi cộng vượt tồn kho
- Product hết hàng
- Inventory bằng 0
- Inventory âm (dữ liệu bất thường)
- Product bị khóa hoặc ngừng kinh doanh (Inactive/Disabled)
- Người dùng gửi productId không tồn tại
- Khả năng Race Condition khi hai request thêm cùng lúc
- Thiếu Transaction nếu nhiều thao tác ghi dữ liệu
- Thiếu Validation đầu vào

Đối với mỗi lỗi cần mô tả:
- Mô tả vấn đề
- Rủi ro
- Hậu quả
- Mức độ nghiêm trọng (Critical/High/Medium/Low)
- Đề xuất cách khắc phục

## Nhiệm vụ 2 - Sinh Business Rules

Sinh danh sách Business Rules theo chuẩn SRS.

Mỗi Business Rule phải gồm:

- Rule ID
- Rule Name
- Description
- Trigger
- Validation
- System Response
- Error Code
- Priority

Bắt buộc phải có các Business Rules cho:

- Quantity phải lớn hơn 0
- Không cho phép quantity âm
- Không cho phép quantity bằng 0
- Không cho phép thêm vượt tồn kho
- Tổng quantity trong giỏ không vượt Inventory
- Không cho thêm sản phẩm hết hàng
- Không cho thêm sản phẩm Inactive
- Product phải tồn tại
- Dữ liệu Inventory phải hợp lệ
- Thao tác Add To Cart phải thực hiện trong Transaction
- Hệ thống phải xử lý đồng thời (Concurrent Update)
- Trả về Error Code rõ ràng cho từng trường hợp

# Định dạng (Output Format)

Xuất kết quả dưới dạng Markdown với cấu trúc:

1. Logic Issues
2. Risk Analysis
3. Business Rules
4. Validation Rules
5. Suggested Patch Summary

Business Rules trình bày dưới dạng bảng Markdown.

Rule ID có định dạng:

BR-CART-001
BR-CART-002
...

Không bỏ sót bất kỳ Edge Case nào.
```

---

## 2. Business Rules do AI trả về

```text
# Business Rules - Cart Module

| Rule ID | Rule Name | Description | Trigger | Validation | System Response | Error Code | Priority |
|----------|-----------|-------------|----------|------------|-----------------|------------|----------|
| BR-CART-001 | Product Must Exist | Chỉ cho phép thêm sản phẩm tồn tại trong hệ thống. | Add To Cart | Kiểm tra productId tồn tại. | Từ chối thêm sản phẩm. | CART_PRODUCT_NOT_FOUND | Critical |
| BR-CART-002 | Quantity Greater Than Zero | Quantity phải lớn hơn 0. | Add To Cart | quantity > 0 | Trả lỗi yêu cầu không hợp lệ. | CART_INVALID_QUANTITY | Critical |
| BR-CART-003 | Reject Negative Quantity | Không cho phép quantity âm. | Add To Cart | quantity >= 1 | Không cập nhật giỏ hàng. | CART_NEGATIVE_QUANTITY | Critical |
| BR-CART-004 | Reject Zero Quantity | Quantity bằng 0 không hợp lệ. | Add To Cart | quantity != 0 | Trả về lỗi validation. | CART_ZERO_QUANTITY | High |
| BR-CART-005 | Product Must Be Active | Chỉ sản phẩm ở trạng thái Active mới được thêm vào giỏ. | Add To Cart | product.status = ACTIVE | Từ chối thêm sản phẩm. | CART_PRODUCT_INACTIVE | High |
| BR-CART-006 | Product Must Be In Stock | Không cho phép thêm sản phẩm đã hết hàng. | Add To Cart | inventory > 0 | Thông báo hết hàng. | CART_OUT_OF_STOCK | Critical |
| BR-CART-007 | Requested Quantity Cannot Exceed Inventory | Số lượng yêu cầu không được vượt quá tồn kho hiện tại. | Add To Cart | quantity <= inventory | Không lưu dữ liệu. | CART_EXCEED_INVENTORY | Critical |
| BR-CART-008 | Total Quantity Cannot Exceed Inventory | Tổng số lượng sau khi cộng vào giỏ không được vượt quá Inventory. | Update Cart Item | existingQuantity + quantity <= inventory | Không cập nhật CartItem. | CART_TOTAL_EXCEED_INVENTORY | Critical |
| BR-CART-009 | Inventory Must Be Valid | Inventory không được âm hoặc không hợp lệ. | Product Validation | inventory >= 0 | Ghi log và từ chối xử lý. | CART_INVALID_INVENTORY | High |
| BR-CART-010 | Transaction Consistency | Thao tác Add To Cart phải thực hiện trong Transaction để đảm bảo tính nhất quán dữ liệu. | Add To Cart | Toàn bộ thao tác thành công hoặc rollback. | Rollback khi có lỗi. | CART_TRANSACTION_FAILED | High |
| BR-CART-011 | Concurrent Update Protection | Hệ thống phải xử lý đồng thời nhiều yêu cầu cập nhật giỏ hàng bằng cơ chế Locking hoặc Optimistic Locking. | Concurrent Requests | Không xảy ra mất dữ liệu khi nhiều request cùng cập nhật. | Chỉ một cập nhật hợp lệ được ghi nhận hoặc retry theo cơ chế đồng bộ. | CART_CONCURRENT_UPDATE | High |
| BR-CART-012 | Input Validation | Tất cả tham số đầu vào phải hợp lệ trước khi xử lý nghiệp vụ. | API Request | userId, productId và quantity không được null hoặc sai định dạng. | Trả lỗi Bad Request. | CART_INVALID_REQUEST | Medium |
| BR-CART-013 | Audit Logging | Mọi thao tác thêm sản phẩm thất bại do vi phạm nghiệp vụ phải được ghi nhật ký phục vụ kiểm tra và truy vết. | Validation Failure | Ghi nhận userId, productId, quantity, thời gian và nguyên nhân lỗi. | Lưu Audit Log. | CART_AUDIT_LOGGED | Medium |
```