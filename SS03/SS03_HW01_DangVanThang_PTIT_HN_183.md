## Đáp án: **B**

Phương án **B** là lựa chọn tối ưu nhất vì nó chứa đầy đủ **5 thành phần cốt lõi của Prompt Engineering** gồm:

| Thành phần                    | Nội dung trong phương án B                                                                                                                                | Vai trò                                                                                                                    |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| **Role (Vai trò)**            | "Hãy đóng vai trò là một Java Developer chuyên nghiệp."                                                                                                   | Định hướng AI hành xử như một lập trình viên có kinh nghiệm, giúp mã nguồn chuẩn hơn về kiến trúc và phong cách lập trình. |
| **Goal (Mục tiêu)**           | "Viết class UserService xử lý đăng ký tài khoản (registerUser)."                                                                                          | Xác định rõ nhiệm vụ cần thực hiện, tránh AI tạo ra những phần không liên quan.                                            |
| **Context (Ngữ cảnh)**        | "Dự án sử dụng Spring Boot 3.x và Java 17."                                                                                                               | Cung cấp môi trường kỹ thuật cụ thể để AI sinh code tương thích đúng framework và phiên bản.                               |
| **Constraint (Ràng buộc)**    | "Phải kiểm tra trùng email qua userRepository.existsByEmail(), nếu trùng ném ra DuplicateEmailException; mật khẩu phải được mã hóa bằng PasswordEncoder." | Mô tả chính xác các quy tắc nghiệp vụ bắt buộc phải tuân thủ.                                                              |
| **Format (Định dạng đầu ra)** | "Trình bày kết quả dưới dạng khối mã nguồn Java kèm chú thích tiếng Việt rõ ràng ở từng bước."                                                            | Kiểm soát hình thức đầu ra, giúp kết quả dễ đọc, dễ hiểu và sẵn sàng sử dụng.                                              |

---

## Vì sao phương án B tối ưu nhất?

Phương án B cung cấp đầy đủ thông tin cần thiết để AI:

1. Hiểu đúng vai trò cần đóng.
2. Hiểu chính xác mục tiêu cần sinh mã.
3. Biết rõ ngữ cảnh công nghệ (Java 17 + Spring Boot 3.x).
4. Tuân thủ đầy đủ nghiệp vụ:

    * Kiểm tra email tồn tại bằng `userRepository.existsByEmail(email)`.
    * Mã hóa mật khẩu bằng `PasswordEncoder`.
    * Ném `DuplicateEmailException` khi email đã tồn tại.
5. Xuất kết quả đúng định dạng mong muốn:

    * Mã Java hoàn chỉnh.
    * Có chú thích tiếng Việt chi tiết.

Điều này giúp giảm tối đa việc phải chỉnh sửa thủ công sau khi AI trả lời.

---

## Phân tích phương án A

### Prompt A

> "Hãy viết code Java Spring Boot xử lý đăng ký tài khoản người dùng, nhớ mã hóa mật khẩu và kiểm tra xem email đã tồn tại chưa."

### Đánh giá theo 5 thành phần

| Thành phần | Có/Không                                  |
| ---------- | ----------------------------------------- |
| Role       | ❌                                         |
| Goal       | ✅                                         |
| Context    | ⚠️ Chỉ nói Spring Boot, chưa rõ phiên bản |
| Constraint | ⚠️ Có nhưng chưa đầy đủ                   |
| Format     | ❌                                         |

### Thiếu sót

#### 1. Thiếu Role

AI không biết nên đóng vai trò gì.

Hệ quả:

* Có thể sinh code ở mức cơ bản.
* Không đảm bảo kiến trúc Service chuẩn.

---

#### 2. Context chưa đầy đủ

Không đề cập:

* Java 17.
* Spring Boot 3.x.

Hệ quả:

* AI có thể dùng API cũ.
* Có thể sinh annotation hoặc cú pháp không phù hợp phiên bản.

---

#### 3. Constraint chưa chi tiết

Chỉ nói:

> "kiểm tra email tồn tại"

Không chỉ rõ:

```java
userRepository.existsByEmail(email)
```

Cũng không yêu cầu:

```java
DuplicateEmailException
```

Hệ quả:

AI có thể:

```java
findByEmail()
```

hoặc:

```java
throw new RuntimeException(...)
```

thay vì yêu cầu đề bài.

---

#### 4. Thiếu Format

Không yêu cầu:

* Chú thích tiếng Việt.
* Cấu trúc OOP.

Hệ quả:

AI có thể trả về code ngắn gọn, thiếu giải thích.

---

## Phân tích phương án C

### Prompt C

> "Tôi có một dự án Spring Boot. Hãy tạo hàm đăng ký tài khoản. Ràng buộc là phải check trùng email và ném lỗi DuplicateEmailException. Định dạng là code Java."

### Đánh giá theo 5 thành phần

| Thành phần | Có/Không       |
| ---------- | -------------- |
| Role       | ❌              |
| Goal       | ✅              |
| Context    | ⚠️ Rất ít      |
| Constraint | ⚠️ Chưa đầy đủ |
| Format     | ✅              |
| Tổng thể   | Chưa đạt       |

---

### Thiếu Role

Không có vai trò cụ thể.

Hệ quả:

AI không được định hướng như một Java Developer chuyên nghiệp.

---

### Context quá ít

Chỉ nói:

> "dự án Spring Boot"

Không nói:

* Java 17
* Spring Boot 3.x

Hệ quả:

Dễ sinh code không phù hợp môi trường.

---

### Constraint chưa đầy đủ

Có:

* Check email.
* Ném DuplicateEmailException.

Nhưng thiếu:

```java
userRepository.existsByEmail(email)
```

và

```java
PasswordEncoder
```

Hệ quả:

AI có thể quên mã hóa mật khẩu, gây lỗi bảo mật nghiêm trọng.

---

## Kết luận

### Đáp án đúng: **B**

Vì đây là phương án duy nhất đáp ứng đầy đủ mô hình:

**Role + Goal + Context + Constraint + Format**

giúp AI sinh mã nguồn chính xác ngay từ lần đầu, giảm thiểu sai lệch nghiệp vụ, sai phiên bản công nghệ và hạn chế tối đa việc chỉnh sửa thủ công sau khi nhận kết quả.