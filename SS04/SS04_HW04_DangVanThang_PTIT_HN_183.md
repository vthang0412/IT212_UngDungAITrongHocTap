## Đáp án: **B**

### Phương án B

> "Hàm fib đệ quy trên có độ phức tạp O(2^N), gây treo hệ thống khi n = 50. Hãy tối ưu lại hàm này sử dụng kỹ thuật Quy hoạch động (Dynamic Programming - Tabulation hoặc Memoization) để đưa độ phức tạp thời gian về O(N) và độ phức tạp không gian O(1) hoặc O(N). Giữ nguyên kiểu trả về long."

---

# Vì sao B là lựa chọn tối ưu nhất?

Đây là một prompt lặp (Iterative Prompting) chất lượng vì nó cung cấp đầy đủ:

### 1. Chỉ rõ vấn đề hiện tại

Prompt xác định nguyên nhân:

```text
Thuật toán đệ quy Fibonacci cổ điển
```

có độ phức tạp:

```text
O(2^N)
```

nên khi:

```java
fib(50)
```

sẽ phát sinh số lượng lời gọi hàm rất lớn.

AI không cần suy đoán nguyên nhân gây chậm.

---

### 2. Chỉ định kỹ thuật cần sử dụng

Prompt yêu cầu rõ:

```text
Dynamic Programming
```

và nêu cụ thể:

* Tabulation
* Memoization

Điều này giúp AI tập trung đúng hướng tối ưu.

---

### 3. Đưa ra mục tiêu định lượng

Prompt không chỉ nói:

> "Làm nhanh hơn"

mà yêu cầu cụ thể:

```text
Time Complexity: O(N)
```

và

```text
Space Complexity: O(1) hoặc O(N)
```

Đây là các tiêu chí kỹ thuật có thể kiểm chứng được.

---

### 4. Có ràng buộc kỹ thuật

Prompt yêu cầu:

```text
Giữ nguyên kiểu trả về long
```

Nhờ đó AI không tự ý thay đổi API của chương trình.

---

### 5. Thể hiện đúng kỹ thuật Iterative Prompting

Quy trình:

* Lượt 1: AI sinh lời giải đệ quy.
* Người dùng đánh giá hiệu năng.
* Người dùng phản hồi bằng yêu cầu cụ thể hơn.
* AI sinh lời giải tối ưu hơn.

Đây chính là bản chất của Prompt lặp.

---

# Nhược điểm của phương án A

### Prompt A

> "Code chạy chậm quá, viết lại bằng thuật toán khác tối ưu hơn giúp tôi."

### Vấn đề

#### Quá mơ hồ

AI không biết:

* Chậm vì nguyên nhân gì.
* Độ phức tạp hiện tại là bao nhiêu.
* Muốn tối ưu đến mức nào.

---

#### Không chỉ định thuật toán

AI có thể:

* Dùng vòng lặp.
* Dùng cache.
* Dùng đa luồng.
* Dùng cách khác bất kỳ.

Kết quả không ổn định.

---

#### Không có ràng buộc

AI có thể:

* Đổi kiểu dữ liệu.
* Đổi chữ ký hàm.
* Thay đổi thiết kế ban đầu.

---

### Kết luận

❌ Có mục tiêu nhưng không đủ chi tiết để đảm bảo kết quả mong muốn.

---

# Nhược điểm của phương án C

### Prompt C

> "Hãy viết lại code tính Fibonacci bằng cách sử dụng Java Stream API để code chạy song song (parallel) cho nhanh hơn."

### Sai trọng tâm bài toán

Nguyên nhân thực sự là:

```text
Thuật toán O(2^N)
```

chứ không phải:

```text
Thiếu đa luồng
```

---

### Parallel không giải quyết gốc rễ

Ví dụ:

```java
fib(50)
```

vẫn tạo ra số lượng lời gọi hàm khổng lồ.

Dù chạy song song thì bản chất vẫn là:

```text
O(2^N)
```

---

### Stream API không phù hợp

Fibonacci là bài toán:

* Dynamic Programming
* Memoization
* Tabulation

chứ không phải bài toán xử lý tập dữ liệu bằng Stream API.

---

### Có thể làm chương trình phức tạp hơn

Parallel Stream:

* Tăng overhead tạo luồng.
* Tăng chi phí quản lý tác vụ.
* Không giải quyết việc tính toán lặp lại.

---

### Kết luận

❌ Tối ưu sai tầng.

❌ Không xử lý nguyên nhân gốc rễ là độ phức tạp thuật toán.

---

# Kết luận cuối cùng

**Đáp án đúng: B**

Vì phương án B:

✅ Xác định chính xác nguyên nhân hiệu năng kém (`O(2^N)`).

✅ Chỉ rõ kỹ thuật cần áp dụng (Dynamic Programming).

✅ Đưa ra mục tiêu cụ thể (`O(N)`).

✅ Có ràng buộc kỹ thuật (giữ nguyên kiểu `long`).

✅ Thể hiện đúng tinh thần **Iterative Prompting**: đánh giá kết quả trước đó và cung cấp hướng dẫn chi tiết để AI cải thiện lời giải.