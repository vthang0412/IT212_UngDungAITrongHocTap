# 1. Thiết kế luồng (What & Why)

## Input

Hệ thống cần nhận các dữ liệu sau:

| Dữ liệu     | Kiểu    | Ý nghĩa                            |
| ----------- | ------- | ---------------------------------- |
| orderAmount | double  | Tổng giá trị đơn hàng (VNĐ)        |
| isWeekend   | boolean | Có phải Thứ 7 hoặc Chủ Nhật không  |
| isVip       | boolean | Khách hàng có thuộc hạng VIP không |

---

## Output

Trả về:

```text
int loyaltyPoints
```

Ý nghĩa:

* Tổng điểm thưởng cuối cùng sau khi áp dụng toàn bộ quy tắc nghiệp vụ.
* Nếu dữ liệu đầu vào không hợp lệ (ví dụ: `orderAmount < 0`) thì ném `IllegalArgumentException`.

---

## Luồng xử lý

```
                +----------------+
                |  orderAmount   |
                +--------+-------+
                         |
                         |
          orderAmount < 0 ?
                /      \
              Yes      No
              |         |
      Throw Exception   |
                        |
                        v
          BasePoints = orderAmount / 100000
                        |
                        |
          Có phải cuối tuần?
                /      \
             Yes       No
              |         |
      BasePoints*=2     |
              \         /
               \       /
                v     v
          Có phải khách VIP?
                /      \
             Yes       No
              |         |
 BasePoints*=1.2        |
              \         /
               \       /
                v     v
        Làm tròn xuống (int)
                |
                v
        Trả về Loyalty Points
```

---

# 2. Workflow / Mega-Prompt

```markdown
# Vai trò (Role)

Bạn là AI Solution Architect kiêm Senior Java Developer với hơn 15 năm kinh nghiệm phát triển hệ thống thương mại điện tử theo chuẩn Enterprise.

# Mục tiêu (Objective)

Hãy xây dựng hoàn chỉnh tính năng Loyalty Points theo chuẩn production.

# Ngữ cảnh (Context)

Business Rule:

1. Cứ mỗi 100.000 VNĐ chi tiêu được 1 điểm.
2. Nếu mua hàng vào Thứ 7 hoặc Chủ Nhật thì nhân đôi tổng điểm.
3. Sau khi áp dụng toàn bộ ưu đãi, nếu khách hàng là VIP thì cộng thêm 20% số điểm.
4. Nếu giá trị đơn hàng âm thì phải ném IllegalArgumentException.
5. Điểm thưởng cuối cùng trả về kiểu int.

# Yêu cầu

Thực hiện lần lượt:

1. Phân tích nghiệp vụ.
2. Thiết kế thuật toán.
3. Sinh class Java hoàn chỉnh.
4. Sinh đầy đủ Javadoc.
5. Comment các đoạn logic quan trọng.
6. Refactor theo Clean Code.
7. Viết Unit Test bằng JUnit 5.

Các Test phải bao phủ tối thiểu:

- Đơn hàng = 0
- Đơn hàng âm
- Đúng 100000
- Không đủ 100000
- Cuối tuần
- VIP
- Cuối tuần + VIP
- Đơn hàng rất lớn
- Boundary Value
- Exception Test

# Ràng buộc

- Java 17
- JUnit 5
- Không dùng Database
- Không dùng Framework
- Clean Code
- SOLID
- Production Ready

# Định dạng Output

Trả lời theo thứ tự:

1. Phân tích nghiệp vụ
2. Thuật toán
3. Java Source
4. Javadoc
5. Unit Test
6. Giải thích từng Test
```

---

# 3. Kiểm chứng thực tế (Text Log)

**User**

> Bạn là AI Solution Architect. Hãy xây dựng tính năng Loyalty Points theo Business Rule sau:
>
> * Cứ 100.000 VNĐ được 1 điểm.
> * Cuối tuần nhân đôi điểm.
> * VIP cộng thêm 20% sau cùng.
> * Đơn hàng âm ném IllegalArgumentException.
> * Sinh Java Source, Javadoc và JUnit 5.

---

**AI**

> Đã phân tích Business Rule.
>
> Thuật toán:
>
> 1. Kiểm tra giá trị đơn hàng.
> 2. Nếu âm → IllegalArgumentException.
> 3. Tính điểm cơ bản = orderAmount / 100000.
> 4. Nếu cuối tuần → nhân đôi.
> 5. Nếu VIP → nhân 1.2.
> 6. Ép kiểu int để lấy điểm cuối cùng.
>
> Sinh mã nguồn Java, Javadoc và bộ Unit Test gồm các trường hợp:
>
> * Giá trị âm
> * Giá trị bằng 0
> * Giá trị 99.999
> * Giá trị đúng 100.000
> * Cuối tuần
> * VIP
> * Cuối tuần + VIP
> * Giá trị lớn
> * Boundary Value
> * Exception Test
>
> Kết quả đáp ứng đúng Business Rule và sẵn sàng tích hợp vào dự án.

---

**User**

> Bộ Unit Test đã bao phủ Edge Cases chưa?

---

**AI**

> Có. Bộ Unit Test bao phủ:
>
> * Normal Case
> * Boundary Value
> * Invalid Input
> * Exception Handling
> * Weekend Rule
> * VIP Rule
> * Weekend + VIP Combination
> * Large Value
> * Zero Value
> * Regression Test

---

**User**

> Logic VIP có áp dụng sau khi nhân đôi cuối tuần không?

---

**AI**

> Có. Thứ tự xử lý đúng theo Business Rule:
>
> 1. Tính điểm cơ bản.
> 2. Nhân đôi nếu mua cuối tuần.
> 3. Cộng thêm 20% nếu là khách VIP.
> 4. Trả về điểm cuối cùng dưới dạng `int`.