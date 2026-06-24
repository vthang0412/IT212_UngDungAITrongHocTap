## 1. Ý đồ thiết kế quy trình 3 bước

Bài toán Event-Driven Architecture không thể giải quyết tốt chỉ bằng một prompt duy nhất vì có 3 lớp vấn đề khác nhau:

* **Bước 1:** Lựa chọn nền tảng truyền sự kiện (Kafka hay RabbitMQ).
* **Bước 2:** Thiết kế độ tin cậy khi có sự cố hệ thống và chống xử lý trùng lặp (Idempotency).
* **Bước 3:** Hiện thực hóa thành mã nguồn Java Spring Boot production-ready.

Quy trình này mô phỏng đúng cách một System Designer và Senior Developer làm việc thực tế:

```text
Business Requirement
        ↓
Broker Selection
        ↓
Failure Analysis
        ↓
Reliability Design
        ↓
Code Implementation
```

---

# 2. Prompt Bước 1 - Lựa chọn Broker

Bạn là Solution Architect chuyên thiết kế hệ thống E-Commerce quy mô lớn.

Bối cảnh:

SpeedyCart sử dụng Event-Driven Architecture.

Sau khi đơn hàng hoàn tất, hệ thống phát sinh sự kiện:

OrderCompletedEvent

Các service tiêu thụ:

* Email Service
* Loyalty Service
* Warehouse Service

Nhiệm vụ:

So sánh hai giải pháp Message Broker:

1. Apache Kafka
2. RabbitMQ

Yêu cầu:

1. Giải thích nguyên lý hoạt động của từng Broker.

2. Lập bảng so sánh:

* Throughput
* Latency
* Scalability
* Durability
* Message Retention
* Consumer Group
* Ordering Guarantee
* Retry Support
* Dead Letter Queue
* Operational Complexity

3. Phân tích hệ thống có:

* 50.000 đơn hàng/ngày
* 500.000 đơn hàng/ngày
* 5.000.000 đơn hàng/ngày

4. Đưa ra kiến trúc đề xuất.

5. Kết luận nên chọn Kafka hay RabbitMQ cho SpeedyCart và giải thích rõ lý do.

---

# 3. Minh chứng chạy thực tế - Bước 1

### Phản hồi AI

## So sánh Kafka và RabbitMQ

| Tiêu chí       | Kafka          | RabbitMQ        |
| -------------- | -------------- | --------------- |
| Throughput     | Rất cao        | Trung bình      |
| Latency        | Thấp           | Thấp            |
| Scalability    | Rất cao        | Cao             |
| Retention      | Có             | Hạn chế         |
| Replay Event   | Có             | Khó             |
| Consumer Group | Có             | Không mạnh bằng |
| Partition      | Có             | Không           |
| DLQ            | Có             | Có              |
| Ordering       | Theo Partition | Theo Queue      |
| Vận hành       | Phức tạp hơn   | Đơn giản hơn    |

### Phân tích tải

#### 50.000 đơn/ngày

```text
RabbitMQ đủ đáp ứng.
```

#### 500.000 đơn/ngày

```text
Kafka bắt đầu có lợi thế.
```

#### 5.000.000 đơn/ngày

```text
Kafka vượt trội về mở rộng ngang.
```

### Kiến trúc đề xuất

```text
Order Service
      ↓
Kafka Topic
      ↓
Email Service

Kafka Topic
      ↓
Loyalty Service

Kafka Topic
      ↓
Warehouse Service
```

### Kết luận

```text
Khuyến nghị sử dụng Apache Kafka.

Lý do:

- Throughput cao.
- Hỗ trợ replay event.
- Consumer Group mạnh.
- Dễ mở rộng theo partition.
- Phù hợp Event-Driven Architecture quy mô lớn.
```

---

# 4. Prompt Bước 2 - What-if Scenario

Tiếp tục với kiến trúc Kafka ở bước trước.

Giả sử:

Email Service bị sập hoàn toàn trong 3 giờ.

Nhiệm vụ:

1. Điều gì xảy ra với các OrderCompletedEvent?

2. Tin nhắn có bị mất không?

3. Khi Email Service hoạt động lại:

* Làm thế nào để tiếp tục xử lý các event chưa xử lý?

4. Làm thế nào để đảm bảo:

Mỗi đơn hàng chỉ gửi đúng 1 email?

5. Giải thích:

* At-least-once Delivery
* Exactly-once Processing
* Idempotency

6. Thiết kế giải pháp:

Redis làm Idempotency Key Store.

7. Minh họa luồng xử lý:

Kafka
↓
Consumer
↓
Redis
↓
Email Service

8. Đề xuất cấu trúc Redis Key.

9. Đề xuất TTL.

10. Phân tích ưu nhược điểm của giải pháp.

---

# 5. Minh chứng chạy thực tế - Bước 2

### Phản hồi AI

## Email Service bị sập 3 giờ

Kafka vẫn giữ event trong topic.

Ví dụ:

```text
Retention = 7 ngày
```

Trong 3 giờ downtime:

```text
Event vẫn nằm trên Broker.
Không bị mất.
```

---

## Khi Email Service hoạt động lại

Consumer tiếp tục đọc từ offset cũ:

```text
Offset 1000
↓
Service crash

Offset 1001..5000
được giữ lại

Service restart
↓
đọc tiếp offset 1001
```

---

## Vấn đề gửi email trùng

Kịch bản:

```text
1. Gửi email thành công
2. Chưa commit offset
3. Consumer crash
4. Kafka gửi lại event
```

Kết quả:

```text
Khách nhận 2 email
```

---

## Giải pháp Redis Idempotency

### Redis Key

```text
email:event:{eventId}
```

Ví dụ:

```text
email:event:ORD-2026-001
```

---

### Luồng xử lý

```text
Kafka Event
      ↓
Consumer
      ↓
Redis EXISTS?
      ↓
YES → Skip
      ↓
NO
      ↓
Send Email
      ↓
Redis SET
      ↓
Commit Offset
```

---

### TTL

```text
30 ngày
```

---

### Ưu điểm

```text
Đơn giản
Nhanh
Chi phí thấp
Chống gửi trùng hiệu quả
```

---

# 6. Prompt Bước 3 - Sinh mã nguồn CoT

Bạn là Senior Java Developer.

Trước khi sinh code hãy suy luận từng bước theo Chain-of-Thought:

Bước 1:
Phân tích luồng xử lý event.

Bước 2:
Xác định vị trí kiểm tra Idempotency.

Bước 3:
Xác định thời điểm commit offset.

Bước 4:
Thiết kế Retry.

Bước 5:
Thiết kế Dead Letter Queue.

Sau đó sinh mã nguồn Java Spring Boot production-ready.

Yêu cầu:

1. Sử dụng:

@KafkaListener

2. Consumer nhận:

OrderCompletedEvent

3. Inject:

RedisTemplate<String, String>

4. Redis Key:

email:event:{eventId}

5. Nếu key đã tồn tại:

* bỏ qua event

6. Nếu chưa tồn tại:

* gửi email

7. Sau khi gửi thành công:

* lưu key vào Redis

8. TTL:

30 ngày

9. Retry:

* tối đa 3 lần

10. Sau 3 lần thất bại:

* gửi sang topic DLQ

11. Logging:

@Slf4j

INFO:

* nhận event
* gửi email thành công
* bỏ qua event trùng

ERROR:

* gửi email thất bại
* chuyển DLQ

12. Trả về:

* OrderEventListener
* KafkaConfig
* RetryConfig
* DLQ Config
* Mã nguồn hoàn chỉnh

---

# 7. Minh chứng chạy thực tế - Bước 3

### Phản hồi AI

## OrderEventListener

```java
import java.time.Duration;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
@Slf4j
public class OrderEventListener {

    private static final String IDEMPOTENCY_PREFIX =
            "email:event:";

    private final RedisTemplate<String, String> redisTemplate;

    private final EmailService emailService;

    private final KafkaTemplate<String, Object> kafkaTemplate;

    @KafkaListener(
            topics = "order-completed",
            groupId = "email-service"
    )
    public void consume(
            OrderCompletedEvent event) {

        String key =
                IDEMPOTENCY_PREFIX +
                event.getEventId();

        log.info(
                "Received event {}",
                event.getEventId()
        );

        Boolean exists =
                redisTemplate.hasKey(key);

        if (Boolean.TRUE.equals(exists)) {

            log.info(
                    "Duplicate event skipped {}",
                    event.getEventId()
            );

            return;
        }

        try {

            emailService.sendOrderCompletedEmail(
                    event
            );

            redisTemplate.opsForValue().set(
                    key,
                    "PROCESSED",
                    Duration.ofDays(30)
            );

            log.info(
                    "Email sent successfully {}",
                    event.getEventId()
            );

        } catch (Exception ex) {

            log.error(
                    "Email sending failed {}",
                    event.getEventId(),
                    ex
            );

            throw ex;
        }
    }
}
```

---

## Retry Configuration

```java
@Configuration
public class KafkaRetryConfig {

    @Bean
    public DefaultErrorHandler errorHandler(
            KafkaTemplate<Object, Object> kafkaTemplate
    ) {

        DeadLetterPublishingRecoverer recoverer =
                new DeadLetterPublishingRecoverer(
                        kafkaTemplate,
                        (record, ex) ->
                                new TopicPartition(
                                        "order-completed-dlq",
                                        record.partition()
                                )
                );

        FixedBackOff backOff =
                new FixedBackOff(
                        2000L,
                        3L
                );

        return new DefaultErrorHandler(
                recoverer,
                backOff
        );
    }
}
```

---

## DLQ Flow

```text
OrderCompletedEvent
        ↓
Kafka Topic
        ↓
Consumer
        ↓
Redis Check
        ↓
Email Service
        ↓
Success
        ↓
Redis Save Key

Failure
        ↓
Retry #1
        ↓
Retry #2
        ↓
Retry #3
        ↓
Dead Letter Queue
```

---

## Kết quả kiến trúc cuối cùng

```text
Order Service
      ↓
Kafka Topic (order-completed)
      ↓
OrderEventListener
      ↓
Redis Idempotency Store
      ↓
Email Service

Failure
      ↓
Retry 3 lần
      ↓
Dead Letter Queue
```

Kiến trúc này đảm bảo:

* API đặt hàng phản hồi nhanh.
* Service hoạt động độc lập.
* Không mất event khi Email Service downtime.
* Không gửi email trùng nhờ Redis Idempotency Key.
* Retry tự động khi Mail Server lỗi.
* Chuyển DLQ sau 3 lần thất bại để dễ giám sát và xử lý sự cố.