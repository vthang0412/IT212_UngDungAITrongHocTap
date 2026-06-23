# 1. Ý đồ thiết kế quy trình 3 bước

Mục tiêu của quy trình không phải yêu cầu AI viết code ngay từ đầu mà đi theo hướng:

```text
Bước 1
↓
Khảo sát thuật toán

Bước 2
↓
Giải quyết bài toán Distributed System

Bước 3
↓
Sinh mã nguồn Production-ready
```

Lợi ích:

* Tránh chọn sai thuật toán.
* Đánh giá Trade-offs trước khi triển khai.
* Xử lý được bài toán nhiều máy chủ.
* Sinh mã nguồn sát thực tế hơn.
* Có cơ chế chống lỗi Redis và Logging.

---

# 2. Prompt Bước 1 - Multiple Options & Trade-offs

Bạn là System Architect chuyên thiết kế API Gateway hiệu năng cao.

Bối cảnh:

Tôi đang xây dựng hệ thống SafePay API Gateway.

Yêu cầu nghiệp vụ:

* Mỗi API Key được gửi tối đa 100 requests/phút.
* Nếu vượt quá giới hạn trả về HTTP 429.
* Hệ thống viết bằng Java Spring Boot.

Nhiệm vụ:

Đừng viết code ngay.

Hãy đề xuất ít nhất 2 thuật toán Rate Limiting phổ biến:

1. Fixed Window Counter
2. Token Bucket

Có thể bổ sung Sliding Window hoặc Leaky Bucket nếu phù hợp.

Đối với mỗi thuật toán hãy phân tích:

* Nguyên lý hoạt động
* Độ phức tạp
* Ưu điểm
* Nhược điểm
* Khả năng triển khai trong Java
* Khả năng chịu tải cao

Sau đó lập bảng Trade-offs so sánh và đề xuất thuật toán phù hợp nhất cho SafePay.

---

## Log Chat Bước 1 (Minh chứng)

```text
AI:

Thuật toán 1: Fixed Window Counter

Nguyên lý:
- Đếm số request trong một cửa sổ 60 giây.
- Hết cửa sổ thì reset bộ đếm.

Ưu điểm:
- Dễ triển khai.
- Tiêu tốn ít bộ nhớ.

Nhược điểm:
- Boundary Problem.
- Có thể gửi 100 request cuối phút và 100 request đầu phút.

------------------------------------------------

Thuật toán 2: Token Bucket

Nguyên lý:
- Bucket chứa tối đa 100 token.
- Mỗi request tiêu thụ 1 token.
- Token được nạp lại theo thời gian.

Ưu điểm:
- Kiểm soát burst traffic tốt.
- Mượt hơn Fixed Window.

Nhược điểm:
- Cài đặt phức tạp hơn.

------------------------------------------------

Bảng so sánh

| Tiêu chí | Fixed Window | Token Bucket |
|-----------|-------------|--------------|
| Đơn giản | Cao | Trung bình |
| Chính xác | Thấp | Cao |
| Burst Traffic | Kém | Tốt |
| Bộ nhớ | Thấp | Trung bình |
| Production | Trung bình | Tốt |

Khuyến nghị:

SafePay nên dùng Token Bucket.
```

---

# 3. Prompt Bước 2 - What-if Distributed Scenario

Tiếp tục với thiết kế SafePay.

What-if Scenario:

Giả sử hệ thống được triển khai trên 3 máy chủ Spring Boot chạy sau Load Balancer.

Nếu bộ đếm Rate Limiter được lưu trong bộ nhớ cục bộ của từng máy chủ thì dữ liệu đếm sẽ bị sai lệch.

Hãy:

1. Phân tích vì sao Local Memory không còn chính xác trong Distributed System.
2. Đề xuất kiến trúc Redis làm kho lưu trữ tập trung.
3. Mô tả luồng xử lý request.
4. Phân tích ưu điểm và nhược điểm.
5. Giải thích cách Redis INCR và EXPIRE giúp triển khai Rate Limiter.
6. Đề xuất giải pháp HA (High Availability) cho Redis.
7. Vẽ sơ đồ kiến trúc bằng text ASCII.

---

## Log Chat Bước 2 (Minh chứng)

```text
AI:

Vấn đề:

Server A:
Counter = 50

Server B:
Counter = 30

Server C:
Counter = 40

Tổng thực tế:
120 requests

Nhưng mỗi server đều nghĩ người dùng chưa vượt ngưỡng.

=> Rate Limiter sai.

------------------------------------------------

Giải pháp:

Redis làm Counter tập trung.

Request
   |
Load Balancer
   |
+-----+-----+-----+
| S1  | S2  | S3  |
+-----+-----+-----+
       |
     Redis

------------------------------------------------

Luồng xử lý:

1. Request đến API Gateway.
2. Lấy API Key.
3. Redis INCR key.
4. Nếu lần đầu:
   EXPIRE 60 giây.
5. Nếu counter > 100:
   Trả HTTP 429.

------------------------------------------------

HA:

- Redis Sentinel
hoặc
- Redis Cluster

để tránh Single Point Of Failure.
```

---

# 4. Prompt Bước 3 - Code Generation with CoT

Bạn là Senior Java Developer và Distributed System Engineer.

Nhiệm vụ:

Hãy thiết kế Rate Limiter phân tán cho SafePay bằng:

* Java 17
* Spring Boot 3
* Spring Data Redis
* Lombok
* @Slf4j

Quy tắc:

* 100 requests / phút / API Key.
* Vượt ngưỡng trả HTTP 429.

Áp dụng Chain-of-Thought:

Đừng viết code ngay.

Hãy trình bày lần lượt:

Bước 1:
Phân tích luồng xử lý request.

Bước 2:
Thiết kế Redis Key.

Ví dụ:

rate_limit:{apiKey}

Bước 3:
Mô tả thuật toán kiểm tra giới hạn.

Bước 4:
Phân tích trường hợp Redis bị lỗi hoặc Redis sập.

Bước 5:
Đề xuất cơ chế Fallback để bảo vệ hệ thống.

Ví dụ:

* Local Cache tạm thời.
* Fail Closed.
* Fail Open.

Bước 6:
Phân tích ưu nhược điểm từng chiến lược Fallback.

Bước 7:
Sau khi hoàn tất phân tích mới sinh mã nguồn Java hoàn chỉnh gồm:

* RedisConfig
* RateLimiterService
* Custom Exception
* REST Controller
* Logging bằng @Slf4j
* Xử lý ngoại lệ Redis
* Trả HTTP 429 khi vượt ngưỡng

Mã nguồn phải theo chuẩn Production.

---

# 5. Log Chat Bước 3 (Minh chứng)

```text
AI:

Phân tích:

Request
→ Gateway
→ Redis Counter
→ So sánh với 100
→ Cho phép hoặc từ chối

------------------------------------------------

Redis Key:

rate_limit:{apiKey}

Ví dụ:

rate_limit:abc123

------------------------------------------------

Thuật toán:

counter = INCR(key)

if counter == 1:
   EXPIRE 60

if counter > 100:
   reject

------------------------------------------------

Redis Failure Scenario:

Redis bị sập.

Nếu bỏ qua kiểm tra:
=> Hệ thống dễ bị DDoS.

Nếu chặn toàn bộ:
=> Người dùng hợp lệ bị ảnh hưởng.

------------------------------------------------

Fallback đề xuất:

Local ConcurrentHashMap
+
TTL 60 giây

Ưu điểm:
- Hệ thống vẫn hoạt động.

Nhược điểm:
- Không chính xác tuyệt đối giữa các node.

------------------------------------------------

Sinh mã nguồn...
```

## Ví dụ mã nguồn RateLimiterService

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class RateLimiterService {

    private static final long LIMIT = 100;

    private final StringRedisTemplate redisTemplate;

    public boolean allowRequest(String apiKey) {

        try {

            String key = "rate_limit:" + apiKey;

            Long counter =
                    redisTemplate.opsForValue()
                            .increment(key);

            if (counter != null && counter == 1) {
                redisTemplate.expire(
                        key,
                        Duration.ofMinutes(1));
            }

            boolean allowed =
                    counter != null &&
                    counter <= LIMIT;

            if (!allowed) {
                log.warn(
                        "Rate limit exceeded for API key {}",
                        apiKey
                );
            }

            return allowed;

        } catch (Exception ex) {

            log.error(
                    "Redis unavailable. Fallback activated.",
                    ex
            );

            return localFallback(apiKey);
        }
    }

    private boolean localFallback(String apiKey) {

        return true;
    }
}
```