## 1. Prompt học tập kiến thức kỹ thuật nâng cao

Bạn là một Senior Java Architect và Spring WebFlux Expert có kinh nghiệm xây dựng hệ thống E-Commerce quy mô lớn.

Mục tiêu:

Tôi đang xây dựng hệ thống SpeedyCart. Sau khi khách hàng đặt hàng thành công, hệ thống cần đồng bộ đơn hàng sang đối tác vận chuyển (GHN, Viettel Post...) thông qua API.

Tôi chưa từng sử dụng Spring WebFlux WebClient nên cần được hướng dẫn từ cơ bản đến nâng cao.

Yêu cầu AI trả lời theo cấu trúc sau:

PHẦN 1 - LEVEL-BASED EXPLANATION

Giải thích cơ chế hoạt động của WebClient ở 2 cấp độ:

Level 1 - Beginner:

* Giải thích bằng ngôn ngữ đơn giản.
* Sử dụng ví dụ đời thực như:

    * Đặt món tại nhà hàng.
    * Gọi điện thoại.
    * Đặt xe công nghệ.
* So sánh Blocking và Non-blocking bằng hình ảnh tư duy dễ hiểu.

Level 2 - Senior Developer:

Giải thích bằng thuật ngữ kỹ thuật:

* Event Loop
* Reactive Streams
* Publisher
* Subscriber
* Backpressure
* Mono
* Flux
* Reactor Netty
* Non-blocking I/O

Mô tả luồng xử lý request-response chi tiết.

---

PHẦN 2 - COMPARATIVE ANALYSIS

Lập bảng so sánh chi tiết:

RestTemplate vs WebClient

Các tiêu chí:

* Blocking/Non-blocking
* Số lượng Thread tiêu thụ
* RAM tiêu thụ
* CPU Utilization
* Throughput
* Scalability
* Learning Curve
* Độ phù hợp với Microservice

Phân tích tình huống:

"Hệ thống xử lý 10.000 request đồng thời"

Ước lượng:

* Số Thread cần thiết
* Tác động tới RAM
* Nguy cơ Thread Starvation

Đưa ra kết luận lựa chọn công nghệ.

---

PHẦN 3 - PRACTICAL IMPLEMENTATION

Sinh mã nguồn Java Spring Boot hoàn chỉnh.

Yêu cầu:

1. Tạo DeliveryIntegrationService

2. Sử dụng Spring WebFlux WebClient

3. Gửi POST Request tới API vận chuyển

4. Sử dụng DTO:

    * DeliveryRequest
    * DeliveryResponse

5. Cấu hình:

    * Connection Timeout = 5 giây
    * Response Timeout = 5 giây

6. Retry:

    * Retry tối đa 3 lần
    * Chỉ retry khi:

        * Timeout
        * ConnectException
        * IOException

7. Logging:

   @Slf4j

   INFO:

    * Bắt đầu gửi đơn hàng
    * Gửi thành công

   ERROR:

    * Retry thất bại
    * API lỗi

8. Sử dụng:

    * Reactor Retry
    * Mono

9. Nếu retry hết:

    * Ném DeliveryIntegrationException

10. Giải thích từng đoạn code.

---

PHẦN 4 - BEST PRACTICES

Liệt kê tối thiểu 10 best practices khi tích hợp API bên thứ ba bằng WebClient trong hệ thống thương mại điện tử production.

---

Định dạng đầu ra:

1. Beginner Explanation
2. Senior Explanation
3. Comparison Table
4. Architecture Flow
5. Java Source Code
6. Best Practices
7. Final Recommendation

---

# 2. Minh chứng chạy thực tế (Mẫu phản hồi AI)

## Beginner Explanation

### Blocking (RestTemplate)

Hãy tưởng tượng bạn vào nhà hàng:

```text
1. Gọi món
2. Đứng chờ đầu bếp nấu
3. Không làm được việc khác
4. Nhận món
5. Rời đi
```

Đây là Blocking.

Thread phải đứng chờ cho tới khi API trả kết quả.

---

### Non-blocking (WebClient)

Ví dụ:

```text
1. Gọi món
2. Nhận số thứ tự
3. Đi làm việc khác
4. Khi món xong sẽ được gọi
5. Quay lại nhận món
```

Đây là Non-blocking.

Thread không cần ngồi chờ.

---

## Senior Developer Explanation

### RestTemplate

Mô hình:

```text
One Request
      ↓
One Thread
      ↓
Wait Response
      ↓
Release Thread
```

Mỗi request giữ một thread trong suốt thời gian chờ I/O.

---

### WebClient

Mô hình:

```text
Request
   ↓
Event Loop
   ↓
Non-blocking Socket
   ↓
Callback
   ↓
Response
```

---

### Reactive Streams

Thành phần:

```text
Publisher
Subscriber
Subscription
Processor
```

---

### Mono

```java
Mono<DeliveryResponse>
```

Đại diện:

```text
0 hoặc 1 kết quả
```

---

### Flux

```java
Flux<Order>
```

Đại diện:

```text
0..N kết quả
```

---

### Event Loop

Một số lượng nhỏ thread xử lý hàng nghìn kết nối I/O.

Ví dụ:

```text
8 Event Loop Thread
↓
10.000 HTTP Connection
```

---

### Backpressure

Cho phép Subscriber kiểm soát tốc độ nhận dữ liệu.

Tránh:

```text
Out Of Memory
```

---

## Comparison Table

| Tiêu chí     | RestTemplate         | WebClient    |
| ------------ | -------------------- | ------------ |
| Mô hình      | Blocking             | Non-blocking |
| Thread       | 1 request = 1 thread | Event Loop   |
| RAM          | Cao                  | Thấp         |
| CPU          | Cao                  | Tối ưu       |
| Throughput   | Trung bình           | Cao          |
| Scalability  | Hạn chế              | Rất cao      |
| Reactive     | Không                | Có           |
| Microservice | Tạm ổn               | Khuyến nghị  |

---

### Khi có 10.000 Request Đồng Thời

#### RestTemplate

```text
10.000 Request
≈ 10.000 Thread
```

Giả sử:

```text
1 Thread ≈ 1MB Stack
```

RAM:

```text
≈ 10GB
```

Nguy cơ:

```text
Thread Starvation
Context Switching
Out Of Memory
```

---

#### WebClient

```text
10.000 Request
≈ 8-32 Event Loop Thread
```

RAM thấp hơn rất nhiều.

---

## Architecture Flow

```text
Order Service
      ↓
DeliveryIntegrationService
      ↓
WebClient
      ↓
GHN / Viettel Post API
      ↓
Mono<DeliveryResponse>
```

---

## DeliveryIntegrationException

```java
public class DeliveryIntegrationException
        extends RuntimeException {

    public DeliveryIntegrationException(
            String message,
            Throwable cause) {
        super(message, cause);
    }
}
```

---

## WebClient Configuration

```java
@Configuration
public class WebClientConfig {

    @Bean
    public WebClient deliveryWebClient() {

        HttpClient httpClient =
                HttpClient.create()
                        .option(
                                ChannelOption.CONNECT_TIMEOUT_MILLIS,
                                5000
                        )
                        .responseTimeout(
                                Duration.ofSeconds(5)
                        );

        return WebClient.builder()
                .clientConnector(
                        new ReactorClientHttpConnector(
                                httpClient
                        )
                )
                .baseUrl("https://api.shipping.com")
                .build();
    }
}
```

---

## DeliveryIntegrationService

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import reactor.core.publisher.Mono;

import reactor.util.retry.Retry;

@Service
@RequiredArgsConstructor
@Slf4j
public class DeliveryIntegrationService {

    private final WebClient deliveryWebClient;

    public Mono<DeliveryResponse> sendOrder(
            DeliveryRequest request) {

        log.info(
                "Sending order to delivery partner. orderId={}",
                request.getOrderId()
        );

        return deliveryWebClient
                .post()
                .uri("/orders")
                .bodyValue(request)
                .retrieve()
                .bodyToMono(
                        DeliveryResponse.class
                )
                .doOnSuccess(response ->
                        log.info(
                                "Delivery order created successfully. trackingCode={}",
                                response.getTrackingCode()
                        )
                )
                .retryWhen(
                        Retry.fixedDelay(
                                        3,
                                        Duration.ofSeconds(1)
                                )
                                .filter(ex ->
                                        ex instanceof java.net.ConnectException
                                                || ex instanceof java.io.IOException
                                                || ex instanceof java.util.concurrent.TimeoutException
                                )
                )
                .doOnError(ex ->
                        log.error(
                                "Delivery API failed after retries",
                                ex
                        )
                )
                .onErrorMap(ex ->
                        new DeliveryIntegrationException(
                                "Cannot connect delivery provider",
                                ex
                        )
                );
    }
}
```

---

## Best Practices

1. Luôn đặt timeout.
2. Không dùng block() trong luồng reactive.
3. Retry có giới hạn.
4. Dùng exponential backoff.
5. Tích hợp Circuit Breaker.
6. Có Correlation ID.
7. Log request/response quan trọng.
8. Dùng DTO riêng cho đối tác.
9. Không expose lỗi đối tác ra client.
10. Theo dõi latency bằng Micrometer.
11. Dùng Connection Pool.
12. Bổ sung Dead Letter Queue khi retry thất bại.

---

## Final Recommendation

Đối với SpeedyCart:

* Không nên dùng RestTemplate cho luồng tích hợp vận chuyển.
* Sử dụng Spring WebClient + Reactor Netty.
* Timeout 5 giây.
* Retry tối đa 3 lần.
* Kết hợp Circuit Breaker (Resilience4j).
* Thực thi bất đồng bộ bằng Mono để không làm chậm luồng đặt hàng của người dùng.

Giải pháp này giúp hệ thống xử lý lượng lớn đơn hàng đồng thời với mức sử dụng Thread và RAM thấp hơn đáng kể so với mô hình Blocking truyền thống.