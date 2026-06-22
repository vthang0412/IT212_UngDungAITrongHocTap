## Đáp án: **B**

Phương án **B** là prompt tối ưu nhất vì chứa đầy đủ 5 thành phần quan trọng của một Prompt hiệu quả: **Vai trò (Role), Mục tiêu (Task), Ngữ cảnh (Context), Ràng buộc (Constraints), Định dạng đầu ra (Output Format)**.

---

# 1. Phân tích phương án B theo 5 thành phần Prompt

### 1. Vai trò (Role)

> "Hãy đóng vai trò là một Java Senior Developer"

AI được định vị là một lập trình viên Java có kinh nghiệm nên sẽ ưu tiên các nguyên tắc:

* Clean Code
* Refactoring
* Maintainability
* Readability

Điều này giúp tăng chất lượng kết quả hơn so với việc chỉ yêu cầu "sửa code".

---

### 2. Mục tiêu (Task)

> "tái cấu trúc (refactor) logic rẽ nhánh lồng nhau phức tạp của class DiscountService trên thành các câu lệnh điều kiện bảo vệ (guard clauses / return sớm)"

Mục tiêu rất rõ ràng:

* Không phải viết lại từ đầu.
* Không phải tối ưu hiệu năng.
* Không phải đổi sang kỹ thuật khác.

AI biết chính xác cần:

* Loại bỏ nested if-else.
* Chuyển sang guard clauses.

---

### 3. Ngữ cảnh (Context)

> "logic rẽ nhánh lồng nhau phức tạp của class DiscountService"

Prompt đã mô tả đúng vấn đề của mã nguồn hiện tại:

* Nhiều if-else lồng nhau.
* Khó đọc.
* Khó bảo trì.

AI hiểu được lý do phải refactor.

---

### 4. Ràng buộc (Constraints)

> "Giữ nguyên logic nghiệp vụ tính chiết khấu ban đầu"

Đây là phần cực kỳ quan trọng.

Ngoài ra còn có:

> "không thay đổi kiểu dữ liệu đầu vào/đầu ra"

và

> "sử dụng Java 11"

Nhờ các ràng buộc này AI sẽ:

* Không đổi chữ ký hàm.
* Không đổi kiểu trả về.
* Không vô tình làm sai nghiệp vụ.
* Không dùng các tính năng vượt quá Java 11.

---

### 5. Định dạng đầu ra (Output Format)

> "Trình bày kết quả dưới dạng khối mã nguồn Java hoàn chỉnh kèm giải thích ngắn bằng tiếng Việt"

Giúp kết quả:

* Dễ đọc.
* Dễ copy.
* Có giải thích để kiểm tra tư duy refactor.

---

# 2. Vì sao phương án B là tối ưu nhất?

Prompt B:

✅ Có vai trò rõ ràng.

✅ Có mục tiêu cụ thể.

✅ Có ngữ cảnh bài toán.

✅ Có ràng buộc bảo toàn nghiệp vụ.

✅ Có định dạng đầu ra.

✅ Nêu đích danh kỹ thuật cần áp dụng: **Guard Clauses**.

✅ Giảm nguy cơ AI hiểu sai yêu cầu.

Đây là một prompt gần với cách làm việc thực tế của Senior Developer khi giao việc cho AI.

---

# 3. Phân tích nhược điểm của phương án A

### Prompt A

> "Tái cấu trúc code Java trên để nó đẹp hơn."

### Thiếu gì?

#### Thiếu Vai trò

Không xác định AI là ai.

---

#### Thiếu Mục tiêu cụ thể

"Đẹp hơn" là khái niệm rất mơ hồ.

AI có thể:

* Đổi tên biến.
* Tách hàm.
* Dùng switch.
* Dùng Strategy Pattern.
* Thêm comment.

Mà không hề dùng Guard Clauses.

---

#### Thiếu Ràng buộc

Không yêu cầu:

* Giữ nguyên nghiệp vụ.
* Giữ nguyên kiểu dữ liệu.
* Giữ nguyên hành vi.

Nguy cơ:

AI tối ưu nhưng làm thay đổi kết quả tính chiết khấu.

---

#### Thiếu Định dạng đầu ra

AI có thể:

* Chỉ giải thích.
* Chỉ đưa pseudo-code.
* Không xuất mã Java hoàn chỉnh.

---

### Kết luận

❌ Prompt quá ngắn.

❌ Mơ hồ.

❌ Dễ phát sinh sai lệch yêu cầu.

---

# 4. Phân tích nhược điểm của phương án C

### Prompt C

> "Hãy sửa code Java này, bỏ bớt if-else lồng nhau đi và sử dụng cấu trúc Java Stream API để viết ngắn gọn nhất có thể."

---

### Sai trọng tâm bài toán

Mục tiêu đề bài:

> Refactor sang Guard Clauses.

Nhưng prompt lại yêu cầu:

> Stream API.

Đây là hai kỹ thuật hoàn toàn khác nhau.

---

### Stream API không phù hợp

Stream API thường dùng cho:

* Collection
* List
* Set
* Map

Ví dụ:

```java
list.stream()
```

Trong bài toán này chỉ là:

```java
if
else
return
```

Không có tập dữ liệu để stream.

---

### Nguy cơ "Code Golf"

Cụm từ:

> "viết ngắn gọn nhất có thể"

dẫn AI tới xu hướng:

* Viết code cực ngắn.
* Dùng toán tử ba ngôi lồng nhau.
* Ghép nhiều điều kiện.

Ví dụ:

```java
return type.equals("VIP")
    ? amount > 1000
        ? isHoliday ? amount * 0.25 : amount * 0.20
        : amount * 0.15
    : amount > 500
        ? isHoliday ? amount * 0.10 : amount * 0.05
        : 0;
```

Code ngắn nhưng:

❌ Khó đọc.

❌ Không đúng tinh thần Clean Code.

❌ Không đúng mục tiêu Guard Clauses.

---

### Thiếu ràng buộc bảo toàn nghiệp vụ

Prompt không nhắc:

* Giữ nguyên logic.
* Giữ nguyên input/output.

Nguy cơ AI thay đổi hành vi.

---

# Kết luận cuối cùng

**Lựa chọn đúng: B**

Vì phương án B đáp ứng đầy đủ 5 thành phần của một Prompt chất lượng:

| Thành phần                       | Có trong B |
| -------------------------------- | ---------- |
| Vai trò (Role)                   | ✅          |
| Mục tiêu (Task)                  | ✅          |
| Ngữ cảnh (Context)               | ✅          |
| Ràng buộc (Constraints)          | ✅          |
| Định dạng đầu ra (Output Format) | ✅          |

Trong khi:

* **A** quá mơ hồ, thiếu hầu hết các thành phần quan trọng.
* **C** yêu cầu kỹ thuật không phù hợp (Stream API), có nguy cơ làm code ngắn nhưng khó đọc và lệch mục tiêu Guard Clauses.