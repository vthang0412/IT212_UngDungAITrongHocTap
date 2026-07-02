## Đáp án lựa chọn

**Chọn: Phương án B**

---

## Lý do lựa chọn

Phương án B là lựa chọn tối ưu vì đáp ứng đầy đủ cấu trúc của một Prompt chất lượng theo mô hình **5 thành phần**.

### 1. Role (Vai trò)

- Prompt chỉ định rõ AI đóng vai **Senior Java Developer**.
- AI sẽ ưu tiên áp dụng các thực hành tốt (Best Practices), viết mã rõ ràng, dễ bảo trì và phù hợp môi trường dự án.
- Kết quả thường có chất lượng và tính chuyên nghiệp cao hơn so với prompt không xác định vai trò.

### 2. Task (Nhiệm vụ)

- Xác định chính xác nhiệm vụ:
  - Viết class `PaymentValidator`.
  - Kiểm tra tính hợp lệ của thẻ tín dụng.
  - Áp dụng thuật toán **Luhn**.
- Không để AI phải suy đoán yêu cầu.

### 3. Context (Ngữ cảnh)

- Cung cấp bối cảnh:
  - Hệ thống **E-commerce**.
  - Validate trước khi gọi API thanh toán.
- Điều này giúp AI hiểu mục đích của đoạn mã và lựa chọn cách triển khai phù hợp với hệ thống thực tế.

### 4. Constraints (Ràng buộc)

Prompt quy định rõ:

- Sử dụng **Java 17**.
- Áp dụng đúng thuật toán **Luhn**.
- Nếu dữ liệu:
  - rỗng
  - hoặc chứa ký tự chữ
    thì phải ném `IllegalArgumentException`.

Các ràng buộc này giúp:

- giảm khả năng AI tự suy diễn;
- tăng tính thống nhất với coding standard của dự án;
- hạn chế lỗi khi tích hợp vào hệ thống.

### 5. Output Format (Định dạng đầu ra)

Prompt yêu cầu:

- Chỉ trả về mã nguồn Java.
- Đặt trong **một code block**.
- Không giải thích.

Nhờ vậy:

- dễ sao chép vào IDE;
- không lẫn văn bản giải thích;
- phù hợp khi sử dụng AI trong quy trình phát triển phần mềm.

---

# Phân tích các phương án bị loại

## Phương án A

Prompt:

> "Viết cho tôi một class Java tên là PaymentValidator để kiểm tra số thẻ tín dụng bằng thuật toán Luhn. Code ngắn gọn thôi nhé."

### Ưu điểm

- Có nêu nhiệm vụ.
- Có chỉ rõ thuật toán Luhn.
- Prompt ngắn và dễ viết.

### Hạn chế

- Không xác định vai trò của AI.
- Không có ngữ cảnh dự án.
- Không quy định phiên bản Java.
- Không yêu cầu xử lý ngoại lệ.
- Không quy định định dạng đầu ra.
- Cụm từ **"Code ngắn gọn thôi"** khá mơ hồ, AI có thể:
  - bỏ qua kiểm tra dữ liệu đầu vào;
  - bỏ xử lý exception;
  - viết code quá tối giản.

### Rủi ro Hallucination / Sai lệch ngữ cảnh

- AI có thể:
  - dùng Java 8 hoặc Java 21 thay vì Java 17;
  - trả về thêm lời giải thích;
  - dùng regex hoặc thư viện khác thay vì triển khai đầy đủ thuật toán Luhn;
  - không xử lý dữ liệu rỗng hoặc ký tự không hợp lệ.

=> Không đảm bảo đúng tiêu chuẩn dự án.

---

## Phương án C

Prompt:

> "Tôi cần một đoạn code kiểm tra thẻ tín dụng. Hãy viết bằng Java và sinh thêm cả database SQL để lưu thông tin thẻ. Nhớ hướng dẫn tôi cách cài đặt database và kết nối JDBC luôn nhé."

### Hạn chế

Prompt bị mở rộng quá nhiều nhiệm vụ không liên quan.

Bao gồm:

- viết Java;
- thiết kế database;
- sinh SQL;
- hướng dẫn cài database;
- hướng dẫn JDBC.

Trong khi yêu cầu nghiệp vụ chỉ cần:

- class `PaymentValidator`;
- thuật toán Luhn.

### Sai lệch ngữ cảnh

Prompt khiến AI chuyển từ:

- **bài toán validate**

sang

- **thiết kế hệ thống lưu trữ thẻ tín dụng**.

Đây là yêu cầu hoàn toàn khác với bài toán ban đầu.

### Rủi ro Hallucination

- AI có thể tự thiết kế bảng dữ liệu không phù hợp.
- Có thể lưu trực tiếp số thẻ tín dụng (vi phạm nguyên tắc bảo mật).
- Có thể hướng dẫn kết nối JDBC dù dự án không yêu cầu.
- Sinh ra lượng lớn thông tin dư thừa, làm giảm chất lượng kết quả chính.

=> Prompt bị lệch mục tiêu và tăng nguy cơ AI suy diễn ngoài phạm vi yêu cầu.