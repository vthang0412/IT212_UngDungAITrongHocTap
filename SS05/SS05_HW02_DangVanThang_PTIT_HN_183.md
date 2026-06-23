## Prompt sau khi tối ưu

Bạn là Solution Architect và Senior Java Developer chuyên thiết kế hệ thống phân tán quy mô lớn.

Bối cảnh:

Tôi đang xây dựng Notification Service bằng Spring Boot để gửi Email và SMS cho khoảng 1.000.000 người dùng khi có chương trình khuyến mãi.

Yêu cầu nghiệp vụ:

* API phản hồi cho người dùng dưới 2 giây.
* Hệ thống phải xử lý bất đồng bộ.
* Có khả năng mở rộng khi số lượng người nhận tăng từ 1 triệu lên 10 triệu người dùng.
* Hạn chế mất dữ liệu khi service bị crash.
* Có cơ chế retry khi gửi Email hoặc SMS thất bại.

Nhiệm vụ:

Đừng viết code ngay.

Hãy thực hiện lần lượt các bước sau:

Bước 1: Đề xuất ít nhất 3 phương án kiến trúc khác nhau (Multiple Options), ví dụ:

* Spring @Async + Thread Pool
* RabbitMQ + Consumer Service
* Apache Kafka + Consumer Group
* Các phương án phù hợp khác

Bước 2: Với mỗi phương án, phân tích:

* Kiến trúc tổng thể
* Luồng xử lý
* Khả năng mở rộng
* Độ phức tạp triển khai
* Chi phí vận hành
* Khả năng chịu tải

Bước 3: Lập bảng Trade-offs so sánh các tiêu chí:

| Tiêu chí               |
| ---------------------- |
| Throughput             |
| Latency                |
| Scalability            |
| Reliability            |
| Retry Mechanism        |
| Monitoring             |
| Operational Complexity |
| Cost                   |

Bước 4: Thực hiện phân tích What-if Scenario:

1. Điều gì xảy ra nếu có 1 triệu email cần gửi trong 5 phút?
2. Điều gì xảy ra nếu SMTP Provider bị lỗi trong 30 phút?
3. Điều gì xảy ra nếu Consumer Service bị crash giữa chừng?
4. Điều gì xảy ra nếu số lượng người dùng tăng lên 10 triệu?
5. Điều gì xảy ra nếu RabbitMQ hoặc Kafka bị đầy queue/topic?

Bước 5: Chọn phương án tối ưu nhất cho hệ thống thực tế và giải thích lý do.

Bước 6: Chỉ sau khi hoàn tất các phân tích trên mới sinh mã nguồn Java Spring Boot minh họa kiến trúc được đề xuất.

## Ví dụ bảng so sánh kiến trúc mà AI có thể sinh ra

```text
+----------------------+------------+------------+------------+
| Tiêu chí             | @Async     | RabbitMQ   | Kafka      |
+----------------------+------------+------------+------------+
| Throughput           | Thấp       | Cao        | Rất cao    |
| Scalability          | Thấp       | Cao        | Rất cao    |
| Retry                | Thủ công   | Tích hợp   | Tích hợp   |
| Persistence          | Không      | Có         | Có         |
| Chịu tải 1M User     | Khó        | Tốt        | Rất tốt    |
| Chịu tải 10M User    | Không phù hợp | Tạm ổn | Tốt nhất   |
| Độ phức tạp          | Thấp       | Trung bình | Cao        |
| Chi phí vận hành     | Thấp       | Trung bình | Cao        |
| Khả năng mở rộng     | Hạn chế    | Tốt        | Xuất sắc   |
+----------------------+------------+------------+------------+

Kết luận:

- @Async phù hợp hệ thống nhỏ.
- RabbitMQ phù hợp hệ thống vừa và lớn.
- Kafka phù hợp hệ thống rất lớn, cần xử lý hàng triệu thông điệp và mở rộng theo chiều ngang.
```