# 1. Giải thích vì sao Prompt cũ thất bại

Prompt cũ:

> **"Tìm lỗi và sửa code giúp tôi."**

### Lý do thất bại

Prompt trên không cung cấp **Business Rule (luật nghiệp vụ)** nên AI chỉ có thể phân tích dựa trên cú pháp và cấu trúc mã nguồn. Với điều kiện:

```java
if (order.getTotalAmount() > 500000)
```

AI không có cơ sở để kết luận toán tử `>` là sai, vì về mặt cú pháp và logic lập trình, biểu thức này hoàn toàn hợp lệ. Do đó, AI thường tập trung vào các vấn đề dễ nhận biết hơn như rút gọn `if-else`, tối ưu cú pháp hoặc cải thiện phong cách viết mã, thay vì phát hiện lỗi nghiệp vụ ẩn.

Muốn AI phát hiện đúng lỗi, cần cung cấp rõ **Business Rule** để AI đối chiếu giữa yêu cầu nghiệp vụ và mã nguồn.

---

# 2. Prompt nâng cao

````markdown
# Vai trò (Role)

Bạn là Senior Java Developer và Software Architect có kinh nghiệm bảo trì hệ thống thương mại điện tử (E-commerce) và chuyên thực hiện Code Review, Bug Analysis và Clean Code Refactoring.

# Mục tiêu (Objective)

Phân tích đoạn mã Java dưới đây để:

- Đối chiếu với Business Rule.
- Phát hiện lỗi logic nghiệp vụ (Business Logic Bug), không chỉ lỗi cú pháp.
- Giải thích nguyên nhân gây lỗi.
- Refactor mã nguồn theo nguyên tắc Clean Code.
- Không làm thay đổi nghiệp vụ đúng.

# Ngữ cảnh (Context)

Module đang xử lý điều kiện miễn phí vận chuyển (Freeship) của hệ thống TechShop.

Business Rule:

- Đơn hàng có giá trị từ **500.000 VNĐ trở lên (>= 500000)** sẽ được miễn phí vận chuyển.
- Đơn hàng nhỏ hơn 500.000 VNĐ phải tính phí vận chuyển.

Hiện khách hàng phản ánh rằng đơn hàng đúng 500.000 VNĐ vẫn bị tính phí.

Đây là đoạn code cần phân tích:

```java
public boolean isFreeShip(Order order) {
    if (order.getTotalAmount() > 500000) {
        return true;
    } else {
        return false;
    }
}
```

# Ràng buộc (Constraints)

- Phải đối chiếu với Business Rule trước khi phân tích.
- Chỉ rõ dòng mã gây lỗi.
- Giải thích tại sao lỗi này ảnh hưởng đến nghiệp vụ.
- Refactor theo Clean Code.
- Loại bỏ if-else không cần thiết.
- Không thay đổi ý nghĩa nghiệp vụ ngoài việc sửa lỗi.
- Đề xuất nếu còn tồn tại rủi ro tiềm ẩn.

# Định dạng đầu ra (Output Format)

1. Tóm tắt Business Rule.
2. Phân tích lỗi logic.
3. Chỉ ra dòng mã sai.
4. Giải thích tác động tới người dùng.
5. Mã nguồn sau khi Refactor.
6. Các đề xuất cải thiện (nếu có).
````

---

# 3. Kết quả Refactor của AI

### Phân tích

**Business Rule**

* Đơn hàng có giá trị **từ 500.000 VNĐ trở lên (>= 500000)** được miễn phí vận chuyển.

**Lỗi logic**

Đoạn mã hiện tại sử dụng:

```java
order.getTotalAmount() > 500000
```

Điều này khiến đơn hàng có giá trị đúng **500.000 VNĐ** không được miễn phí vận chuyển, trái với yêu cầu nghiệp vụ.

**Dòng gây lỗi**

```java
order.getTotalAmount() > 500000
```

Toán tử `>` phải được sửa thành `>=`.

**Tác động**

* Khách hàng có đơn hàng đúng 500.000 VNĐ vẫn bị tính phí ship.
* Khiếu nại từ khách hàng.
* Sai chính sách khuyến mại của hệ thống.
* Ảnh hưởng đến uy tín doanh nghiệp.

---

# 4. Code sau khi Refactor

```java
public boolean isFreeShip(Order order) {
    // Miễn phí vận chuyển khi tổng giá trị đơn hàng từ 500.000 VNĐ trở lên.
    return order.getTotalAmount() >= 500000;
}
```

---

## Đề xuất cải thiện

* Tránh sử dụng "magic number", nên khai báo hằng số:

```java
private static final double FREESHIP_THRESHOLD = 500000;
```

Sau đó sử dụng:

```java
public boolean isFreeShip(Order order) {
    return order.getTotalAmount() >= FREESHIP_THRESHOLD;
}
```

Cách này giúp mã nguồn dễ bảo trì, dễ thay đổi khi chính sách miễn phí vận chuyển được cập nhật trong tương lai và tuân thủ nguyên tắc **Clean Code**.