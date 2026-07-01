## Đáp án

**Chọn phương án B.**

---

# Lý do chọn phương án B

**Khởi tạo workspace trong Antigravity (version 1.23+), index toàn bộ thư mục Guai-api và sử dụng prompt phân tích toàn bộ luồng Checkout Flow.**

### Lý do

- Antigravity có khả năng **index toàn bộ source code**, giúp AI hiểu được cấu trúc của cả dự án thay vì chỉ từng file riêng lẻ.

- AI có thể lần theo toàn bộ luồng xử lý:
   - Controller
   - Service
   - Repository
   - JWT Security Filter
   - Các lớp DTO, Entity nếu cần

- Có khả năng **theo dõi call graph** giữa các class và method, nên hiểu đúng nghiệp vụ xuyên suốt nhiều tầng của hệ thống.

- Prompt yêu cầu AI đóng vai **System Analyst**, vì vậy đầu ra không chỉ là giải thích code mà còn được tổng hợp thành tài liệu SRS đúng cấu trúc:
   - Pre-conditions
   - Main Flow
   - Exceptions

- AI không cần người dùng chỉ định từng file liên quan vì đã có toàn bộ context của workspace.

- Phù hợp với các dự án enterprise có hàng trăm hoặc hàng nghìn file Java.

- Giảm đáng kể thời gian đọc source code thủ công.

- Hạn chế tối đa việc bỏ sót logic nghiệp vụ nằm rải rác ở nhiều package khác nhau.

- Đây chính là điểm mạnh được học trong **Session 10**:
   - Workspace Indexing
   - Project Context
   - Cross-file Reasoning
   - Large Codebase Analysis

---

# Lý do loại trừ phương án A

## Phương án A

*Mở từng file trong VSCode và dùng Github Copilot Chat giải thích từng đoạn code.*

### Hạn chế

- Copilot chủ yếu phân tích **file đang mở** hoặc vùng code được chọn.

- Không tự hiểu toàn bộ luồng nghiệp vụ của dự án.

- Người dùng phải tự:
   - tìm Controller
   - tìm Service
   - tìm Repository
   - tìm Filter
   - ghép các thông tin lại.

- Dễ bỏ sót các method được gọi gián tiếp.

- Không tự tổng hợp được tài liệu SRS hoàn chỉnh.

### Rủi ro Context Loss

- AI chỉ nhìn thấy từng phần code.

- Không biết mối liên hệ giữa các tầng.

- Có thể giải thích đúng từng method nhưng sai hoặc thiếu nghiệp vụ tổng thể.

- Khi cuộc hội thoại kéo dài, AI khó nhớ các file đã phân tích trước đó.

---

# Lý do loại trừ phương án C

## Phương án C

*Copy thủ công 5 file Java rồi dán vào ChatGPT/Gemini.*

### Hạn chế

- Phải tự xác định chính xác những file liên quan.

- Nếu thiếu chỉ một file thì AI sẽ không hiểu đầy đủ Checkout Flow.

- Không có khả năng truy cập toàn bộ project.

- Không thể tự lần theo dependency hoặc các class được gọi ngoài phạm vi được cung cấp.

- Việc copy code rất mất thời gian.

- Dự án lớn dễ vượt giới hạn độ dài context.

### Rủi ro Context Loss

- Dễ thiếu:
   - Security Filter
   - Utility class
   - Config
   - Exception Handler
   - Repository khác
   - Service phụ

- Nếu đoạn code quá dài phải chia nhiều lần gửi, AI có thể quên nội dung ở các tin nhắn trước.

- Logic nghiệp vụ bị đứt đoạn do không có đầy đủ ngữ cảnh của toàn bộ hệ thống.