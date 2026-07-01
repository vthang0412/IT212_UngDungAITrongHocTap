# 1. Thiết kế luồng xử lý (Input / Output / Sơ đồ)

### Mục tiêu

Khi người dùng đang xem chi tiết một sản phẩm, hệ thống sẽ tự động đề xuất các sản phẩm phù hợp dựa trên:

* Lịch sử mua hàng
* Danh mục sản phẩm
* Hành vi xem sản phẩm
* Độ tương đồng giữa các sản phẩm
* Tình trạng còn hàng

---

### Input

| Input               | Mô tả                       |
| ------------------- | --------------------------- |
| userId              | ID người dùng               |
| productId           | ID sản phẩm đang xem        |
| purchaseHistory     | Lịch sử mua hàng            |
| viewHistory         | Lịch sử xem                 |
| categoryId          | Danh mục sản phẩm           |
| inventory           | Tồn kho                     |
| recommendationLimit | Số lượng sản phẩm cần gợi ý |

---

### Output

| Output               | Mô tả                    |
| -------------------- | ------------------------ |
| recommendedProducts  | Danh sách sản phẩm gợi ý |
| recommendationScore  | Điểm phù hợp             |
| recommendationReason | Lý do gợi ý              |
| generatedTime        | Thời điểm sinh kết quả   |

---

### Luồng xử lý (ASCII)

```text
+--------------------+
| User mở Product    |
+---------+----------+
          |
          v
Lấy Product đang xem
          |
          v
Kiểm tra User Login?
      /          \
    Yes          No
    |             |
    |             |
Lấy Purchase      Chỉ dùng
History           Product Similarity
    |
    v
Lấy View History
    |
    v
Phân tích sở thích
    |
    v
Lọc sản phẩm:
- Cùng Category
- Còn hàng
- Active
- Không mua gần đây
    |
    v
Tính Recommendation Score
    |
    v
Sắp xếp theo Score
    |
    v
Lấy Top N sản phẩm
    |
    v
Trả kết quả Recommendation API
```

---

# 2. Workflow Prompt (Mega Prompt)

```text
# Vai trò (Role)

Bạn là một Senior Business Analyst kiêm Solution Architect với hơn 15 năm kinh nghiệm phân tích nghiệp vụ và xây dựng tài liệu SRS theo chuẩn IEEE 830/ISO 29148. Bạn có kiến thức về AI Recommendation System, Machine Learning và hệ thống thương mại điện tử.

# Mục tiêu (Objective)

Từ yêu cầu thô của khách hàng:

"Tôi muốn hệ thống tự động gợi ý các sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ."

Hãy phân tích và xây dựng đầy đủ tài liệu SRS cho module "AI Product Recommendation".

# Ngữ cảnh (Context)

Hệ thống Shop AI gồm:

- User
- Product
- Category
- Order
- Order Detail
- Cart
- Inventory
- Authentication (JWT)
- RBAC

Module Recommendation sẽ được gọi khi người dùng mở trang chi tiết sản phẩm.

Nguồn dữ liệu sử dụng:

- Purchase History
- View History
- Product Category
- Inventory
- Product Status
- Recommendation Score

# Ràng buộc (Constraints)

Thực hiện lần lượt các nhiệm vụ sau:

## Bước 1

Phân tích yêu cầu nghiệp vụ.

Xác định:

- Business Goal
- Actors
- Trigger
- Preconditions
- Postconditions

## Bước 2

Sinh Use Case Description gồm:

- Use Case ID
- Use Case Name
- Description
- Primary Actor
- Main Flow
- Alternative Flow
- Exception Flow

## Bước 3

Sinh Functional Requirements.

Mỗi Functional Requirement gồm:

- Requirement ID
- Name
- Description
- Input
- Output
- Validation
- Business Rule
- Exception

Bao gồm các trường hợp:

- User chưa đăng nhập
- Không có lịch sử mua
- Không có sản phẩm tương tự
- Product hết hàng
- Product Inactive
- Recommendation Engine lỗi

## Bước 4

Sinh Data Dictionary sơ bộ.

Bao gồm:

- User
- Product
- Order
- OrderDetail
- Recommendation
- RecommendationHistory

Mỗi bảng cần:

- Field
- Data Type
- Description
- Constraint

## Bước 5

Đề xuất API.

Ví dụ:

GET /products/{id}/recommendations

Bao gồm:

- Request
- Response
- Status Code

# Định dạng (Output Format)

Xuất Markdown.

Các mục:

1. Business Analysis
2. Use Case Description
3. Functional Requirements
4. Business Rules
5. Data Dictionary
6. API Specification
7. Assumptions
8. Future Enhancements

Không bỏ sót Edge Cases.
```

---

# 3. Minh chứng chạy thực tế (Text Log)

```text
=== User ===

Tôi muốn hệ thống tự động gợi ý các sản phẩm liên quan khi người dùng đang xem một món đồ, dựa trên lịch sử mua hàng của họ.

=== Assistant ===

Đã phân tích yêu cầu.

Business Goal:
- Tăng tỷ lệ chuyển đổi mua hàng.
- Cá nhân hóa trải nghiệm người dùng.

Actors:
- Customer
- Recommendation Engine

Use Case:
UC-REC-001

Tên:
Xem gợi ý sản phẩm thông minh.

Main Flow:
1. Người dùng mở trang chi tiết sản phẩm.
2. Hệ thống kiểm tra trạng thái đăng nhập.
3. Thu thập lịch sử mua hàng và lịch sử xem.
4. Lọc các sản phẩm cùng danh mục còn hàng và đang hoạt động.
5. Tính Recommendation Score dựa trên mức độ liên quan.
6. Sắp xếp theo điểm phù hợp.
7. Trả về Top N sản phẩm gợi ý.

Alternative Flow:
- Người dùng chưa đăng nhập: chỉ gợi ý sản phẩm tương tự theo danh mục.
- Không có lịch sử mua hàng: ưu tiên sản phẩm phổ biến và cùng danh mục.

Exception Flow:
- Sản phẩm không tồn tại.
- Sản phẩm đã ngừng kinh doanh.
- Hết hàng.
- Recommendation Engine không phản hồi.
- Không tìm thấy sản phẩm phù hợp.

Functional Requirements:

FR-REC-001:
Hệ thống phải tự động sinh danh sách sản phẩm gợi ý khi người dùng xem chi tiết sản phẩm.

FR-REC-002:
Hệ thống phải ưu tiên lịch sử mua hàng để tính điểm Recommendation Score.

FR-REC-003:
Nếu người dùng chưa đăng nhập, hệ thống chỉ sử dụng thông tin sản phẩm hiện tại và danh mục để gợi ý.

FR-REC-004:
Không đề xuất sản phẩm hết hàng hoặc Inactive.

FR-REC-005:
Cho phép cấu hình số lượng sản phẩm gợi ý (Top N).

Data Dictionary:

User
- userId
- fullName
- email

Product
- productId
- productName
- categoryId
- inventory
- status

Recommendation
- recommendationId
- userId
- productId
- score
- generatedTime

RecommendationHistory
- historyId
- userId
- viewedProductId
- recommendedProductId
- createdAt

API:

GET /products/{id}/recommendations

Response:

HTTP 200 OK

{
  "productId": 100,
  "recommendations": [
    {
      "productId": 201,
      "score": 0.96,
      "reason": "Frequently purchased together"
    },
    {
      "productId": 202,
      "score": 0.91,
      "reason": "Same category"
    }
  ]
}

Business Rules:

BR-001:
Chỉ gợi ý sản phẩm còn hàng.

BR-002:
Không gợi ý sản phẩm Inactive.

BR-003:
Không gợi ý sản phẩm người dùng vừa mua trong thời gian cấu hình.

BR-004:
Recommendation Score được sắp xếp giảm dần.

BR-005:
Kết quả trả về tối đa Top N sản phẩm theo cấu hình hệ thống.
```