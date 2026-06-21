# Đáp án: **C**

## Vì sao phương án C là đáp án đúng?

Phương án C thể hiện chính xác quy trình **Iterative Prompting (Prompt lặp và tối ưu)** đã học:

### Bước 1: Đánh giá kết quả hiện tại (Evaluate)

AI trả về thuật toán:

```java
for (int i = 2; i < n; i++) {
    if (n % i == 0) {
        return false;
    }
}
```

Thuật toán này:

* Đúng về mặt logic.
* Cho kết quả chính xác.
* Nhưng chưa tối ưu hiệu năng.

Người dùng nhận diện được chất lượng đầu ra chưa đạt yêu cầu.

---

### Bước 2: Nhận diện vấn đề (Identify)

Phân tích cụ thể:

* Vòng lặp chạy từ `2 → n - 1`.
* Độ phức tạp:

```text
O(n)
```

Khi kiểm tra số lớn:

```text
n = 1.000.000.007
```

sẽ phải thực hiện rất nhiều phép chia.

Người dùng chỉ rõ lỗi:

> "Vòng lặp chạy đến n-1 quá chậm đối với số lớn."

Đây là bước quan trọng nhất của Iterative Prompting.

---

### Bước 3: Tinh chỉnh Prompt (Refine)

Người dùng bổ sung yêu cầu:

> "Hãy sửa thuật toán chỉ kiểm tra đến căn bậc hai của n bằng Math.sqrt(n)."

Ví dụ:

```java
for (int i = 2; i <= Math.sqrt(n); i++) {
    if (n % i == 0) {
        return false;
    }
}
```

Độ phức tạp giảm xuống:

```text
O(√n)
```

Ví dụ:

| n         | Số lần lặp O(n) | Số lần lặp O(√n) |
| --------- | --------------- | ---------------- |
| 100       | 99              | 10               |
| 10.000    | 9.999           | 100              |
| 1.000.000 | 999.999         | 1.000            |

Hiệu năng được cải thiện đáng kể.

---

## Mối liên hệ với Iterative Prompting

Quy trình đúng:

```text
Prompt ban đầu
        ↓
AI trả lời
        ↓
Đánh giá kết quả
        ↓
Nhận diện điểm chưa đạt
        ↓
Bổ sung yêu cầu cụ thể
        ↓
AI sinh phiên bản tốt hơn
```

Đó chính là bản chất của Iterative Prompting:

> Không mong đợi AI trả lời hoàn hảo ngay lần đầu, mà liên tục cải tiến đầu ra thông qua phản hồi có mục tiêu.

---

# Vì sao phương án A sai?

### Phương án A

> "Bỏ cuộc và tự mình viết lại code tối ưu bằng tay."

Vấn đề:

* Không tận dụng được khả năng hội thoại của AI.
* Không có bước phản hồi để AI cải thiện kết quả.
* Bỏ qua toàn bộ quy trình:

```text
Đánh giá
→ Nhận diện lỗi
→ Tinh chỉnh
```

Đây không phải Iterative Prompting.

---

# Vì sao phương án B sai?

### Phương án B

> "Mở chat mới rồi dán lại prompt cũ nhưng đổi chữ hoa chữ thường."

Vấn đề:

### Không giữ ngữ cảnh

Khi mở chat mới:

* AI không biết kết quả trước đó.
* AI không biết điểm nào chưa đạt.

Toàn bộ quá trình đánh giá bị mất.

---

### Không chỉ ra lỗi cụ thể

Prompt vẫn giữ nguyên ý nghĩa.

AI không nhận được thông tin:

```text
- Thuật toán hiện tại O(n)
- Cần O(√n)
- Dùng Math.sqrt()
```

Do đó khả năng cao AI sẽ trả về kết quả tương tự.

---

### Thay đổi chữ hoa/chữ thường không tạo ra cải tiến có mục tiêu

Ví dụ:

```text
Viết hàm kiểm tra số nguyên tố
```

và

```text
VIẾT HÀM KIỂM TRA SỐ NGUYÊN TỐ
```

gần như không cung cấp thêm thông tin kỹ thuật nào để AI tối ưu thuật toán.

---

# Kết luận

✅ **Đáp án đúng: C**

Vì phương án C thực hiện đầy đủ tư duy **Iterative Prompting**:

1. **Đánh giá** đầu ra hiện tại.
2. **Nhận diện** chính xác điểm chưa tối ưu.
3. **Tinh chỉnh** prompt bằng yêu cầu kỹ thuật cụ thể.
4. **Tận dụng ngữ cảnh của phiên chat hiện tại** để AI cải thiện kết quả ở vòng tiếp theo.

Trong khi đó:

* **A**: bỏ qua quá trình cải tiến.
* **B**: mất ngữ cảnh và không cung cấp phản hồi hữu ích.

Do đó **C là lựa chọn duy nhất phản ánh đúng quy trình Iterative Prompting (Evaluate → Identify → Refine).**