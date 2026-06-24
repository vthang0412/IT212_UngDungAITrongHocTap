## 1. Phân tích bối cảnh

Bài toán giỏ hàng (Shopping Cart) có đặc điểm:

* Tần suất đọc/ghi rất lớn (add, remove, update quantity liên tục).
* Dữ liệu mang tính tạm thời nhưng ảnh hưởng trực tiếp đến trải nghiệm mua hàng.
* Nếu lưu hoàn toàn trên SQL Database sẽ gây nhiều truy vấn UPDATE/INSERT liên tục dẫn đến nghẽn I/O.
* Cần đánh giá nhiều lựa chọn công nghệ thay vì chỉ một giải pháp duy nhất.
* Cần xem xét các tình huống sự cố thực tế (What-if Scenario) như Redis bị crash để tránh mất dữ liệu giỏ hàng.

Do đó prompt cần được thiết kế đa chiều gồm:

1. Vai trò chuyên gia (System Architect).
2. Mục tiêu tối ưu hiệu năng.
3. So sánh nhiều phương án (Multiple Options).
4. Phân tích đánh đổi (Trade-offs).
5. Xử lý tình huống giả định (What-if) và cung cấp mã triển khai thực tế.

---

## 2. Prompt tối ưu

Bạn là một Senior System Architect có kinh nghiệm thiết kế hệ thống E-Commerce quy mô lớn.

Bối cảnh:

Hệ thống SpeedyCart đang gặp vấn đề hiệu năng đối với chức năng Shopping Cart. Hiện tại mọi thao tác thêm sản phẩm, xóa sản phẩm hoặc cập nhật số lượng đều được ghi trực tiếp xuống SQL Database khiến độ trễ tăng cao và hệ thống bị nghẽn khi lượng người dùng đồng thời lớn.

Nhiệm vụ:

Hãy phân tích và đề xuất giải pháp lưu trữ dữ liệu giỏ hàng tối ưu.

Yêu cầu 1 - Multiple Options:

Đề xuất tối thiểu 3 phương án lưu trữ dữ liệu giỏ hàng tạm thời, ví dụ:

* SQL Database
* Redis In-Memory Cache
* Client-side Session/Cookie

Có thể đề xuất thêm các phương án khác nếu phù hợp.

Yêu cầu 2 - Trade-offs:

Lập bảng so sánh chi tiết các phương án theo các tiêu chí:

* Tốc độ đọc
* Tốc độ ghi
* Tính đồng nhất dữ liệu
* Khả năng chịu lỗi
* Nguy cơ mất dữ liệu
* Khả năng mở rộng
* Độ phức tạp triển khai
* Chi phí vận hành

Sau bảng, hãy đưa ra nhận xét ưu nhược điểm của từng giải pháp.

Yêu cầu 3 - Đề xuất kiến trúc:

Đề xuất kiến trúc tối ưu cho SpeedyCart khi có hàng chục nghìn người dùng đồng thời.

Mô tả luồng dữ liệu từ:

User → Application Server → Redis → SQL Database

Giải thích vì sao kiến trúc đó phù hợp.

Yêu cầu 4 - What-if Scenario:

Phân tích chi tiết tình huống:

"Redis Cluster đột ngột bị crash hoặc mất nguồn điện."

Hãy trả lời:

1. Điều gì sẽ xảy ra với dữ liệu giỏ hàng?
2. Những dữ liệu nào có nguy cơ bị mất?
3. Redis Persistence RDB hoạt động như thế nào?
4. Redis Persistence AOF hoạt động như thế nào?
5. So sánh RDB và AOF.
6. Đề xuất cấu hình kết hợp RDB + AOF.
7. Thiết kế cơ chế fallback ghi tạm xuống SQL Database để giảm nguy cơ mất dữ liệu.
8. Mô tả quy trình khôi phục dữ liệu sau khi Redis hoạt động trở lại.

Yêu cầu 5 - Implementation:

Sinh mã nguồn Java Spring Boot hoàn chỉnh cho:

* RedisConfig
* RedisConnectionFactory
* RedisTemplate<String, Object>
* Cấu hình serializer an toàn
* Timeout kết nối
* Password Redis
* Ví dụ service lưu và đọc Shopping Cart

Yêu cầu định dạng kết quả:

1. Executive Summary
2. Bảng so sánh công nghệ
3. Kiến trúc đề xuất
4. Phân tích What-if Scenario
5. Source code Spring Boot
6. Kết luận cuối cùng về giải pháp phù hợp nhất cho SpeedyCart

---

## 3. Minh chứng chạy thực tế (mẫu phản hồi AI)

### Executive Summary

Đối với hệ thống thương mại điện tử có lượng truy cập lớn, Redis là lựa chọn tối ưu để lưu trữ Shopping Cart do khả năng đọc/ghi trong bộ nhớ với độ trễ rất thấp. Tuy nhiên Redis cần được cấu hình Persistence và cơ chế fallback xuống SQL để tránh mất dữ liệu khi xảy ra sự cố.

---

### Bảng so sánh công nghệ

| Tiêu chí               | SQL Database | Redis                 | Session/Cookie |
| ---------------------- | ------------ | --------------------- | -------------- |
| Tốc độ đọc             | Trung bình   | Rất cao               | Rất cao        |
| Tốc độ ghi             | Trung bình   | Rất cao               | Cao            |
| Đồng nhất dữ liệu      | Cao          | Trung bình            | Thấp           |
| Khả năng mở rộng       | Trung bình   | Cao                   | Cao            |
| Chịu lỗi               | Cao          | Phụ thuộc Persistence | Thấp           |
| Nguy cơ mất dữ liệu    | Thấp         | Trung bình            | Cao            |
| Độ phức tạp triển khai | Thấp         | Trung bình            | Thấp           |
| Chi phí vận hành       | Trung bình   | Trung bình-Cao        | Thấp           |

### Nhận xét

**SQL Database**

* Ưu điểm:

    * ACID.
    * Dữ liệu bền vững.
* Nhược điểm:

    * Chậm khi ghi liên tục.

**Redis**

* Ưu điểm:

    * Độ trễ thấp.
    * Hàng trăm nghìn request/giây.
* Nhược điểm:

    * Có thể mất dữ liệu nếu không bật Persistence.

**Session/Cookie**

* Ưu điểm:

    * Không cần server-side storage.
* Nhược điểm:

    * Giới hạn dung lượng.
    * Không phù hợp cho hệ thống lớn.

---

### Kiến trúc đề xuất

```text
User
  ↓
Load Balancer
  ↓
Application Server
  ↓
Redis Cluster
  ↓ (backup/fallback)
SQL Database
```

Luồng xử lý:

1. User cập nhật giỏ hàng.
2. Dữ liệu ghi vào Redis.
3. Redis trả kết quả ngay.
4. Background Job đồng bộ xuống SQL.
5. SQL đóng vai trò nguồn dữ liệu dự phòng.

---

## What-if Scenario

### Redis bị crash

Hậu quả:

* Dữ liệu chỉ tồn tại trong RAM có thể mất.
* Người dùng có thể thấy giỏ hàng rỗng sau khi hệ thống khởi động lại.

### Redis RDB

RDB tạo snapshot định kỳ:

```text
save 900 1
save 300 10
save 60 10000
```

Ưu điểm:

* Khôi phục nhanh.
* File nhỏ.

Nhược điểm:

* Có thể mất dữ liệu kể từ snapshot cuối cùng.

---

### Redis AOF

Mọi lệnh ghi được append vào file log:

```text
appendonly yes
appendfsync everysec
```

Ưu điểm:

* Giảm nguy cơ mất dữ liệu.

Nhược điểm:

* Tốn dung lượng hơn.

---

### So sánh

| Tiêu chí        | RDB       | AOF        |
| --------------- | --------- | ---------- |
| Tốc độ          | Cao       | Trung bình |
| Mất dữ liệu     | Nhiều hơn | Ít hơn     |
| Khôi phục       | Nhanh     | Chậm hơn   |
| Kích thước file | Nhỏ       | Lớn        |

---

### Khuyến nghị

Sử dụng:

```text
RDB + AOF
```

Cấu hình:

```properties
save 900 1
save 300 10
save 60 10000

appendonly yes
appendfsync everysec
```

---

### Fallback SQL

Khi ghi giỏ hàng:

```text
1. Ghi Redis
2. Ghi hàng đợi sự kiện
3. Worker đồng bộ SQL
4. Redis crash
5. Khôi phục dữ liệu từ SQL
```

---

## Spring Boot RedisConfig

```java
@Configuration
public class RedisConfig {

    @Value("${spring.data.redis.host}")
    private String host;

    @Value("${spring.data.redis.port}")
    private int port;

    @Value("${spring.data.redis.password}")
    private String password;

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {

        RedisStandaloneConfiguration config =
                new RedisStandaloneConfiguration();

        config.setHostName(host);
        config.setPort(port);
        config.setPassword(password);

        LettuceClientConfiguration clientConfig =
                LettuceClientConfiguration.builder()
                        .commandTimeout(Duration.ofSeconds(3))
                        .shutdownTimeout(Duration.ZERO)
                        .build();

        return new LettuceConnectionFactory(config, clientConfig);
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate(
            RedisConnectionFactory factory) {

        RedisTemplate<String, Object> template =
                new RedisTemplate<>();

        template.setConnectionFactory(factory);

        template.setKeySerializer(
                new StringRedisSerializer());

        template.setHashKeySerializer(
                new StringRedisSerializer());

        GenericJackson2JsonRedisSerializer serializer =
                new GenericJackson2JsonRedisSerializer();

        template.setValueSerializer(serializer);
        template.setHashValueSerializer(serializer);

        template.afterPropertiesSet();

        return template;
    }
}
```

### Service Shopping Cart

```java
@Service
@RequiredArgsConstructor
public class CartService {

    private final RedisTemplate<String, Object> redisTemplate;

    public void saveCart(Long userId, Object cart) {

        String key = "cart:" + userId;

        redisTemplate.opsForValue()
                .set(key, cart, Duration.ofDays(7));
    }

    public Object getCart(Long userId) {

        String key = "cart:" + userId;

        return redisTemplate.opsForValue()
                .get(key);
    }

    public void removeCart(Long userId) {

        redisTemplate.delete("cart:" + userId);
    }
}
```