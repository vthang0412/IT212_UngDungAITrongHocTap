Để tối ưu prompt gỡ lỗi, nên áp dụng cấu trúc **5 thành phần**:

1. **Role (Vai trò)**
2. **Context (Ngữ cảnh)**
3. **Input Data (Dữ liệu đầu vào)**
4. **Task/Constraints (Nhiệm vụ & Ràng buộc)**
5. **Output Format (Định dạng đầu ra)**

## Prompt sau khi tối ưu

Bạn là một Java Debugger cấp cao, chuyên phân tích Stack Trace, tìm Root Cause và đề xuất giải pháp sửa lỗi an toàn theo nguyên tắc OOP và Clean Code.

[Bối cảnh]

Tôi đang chạy thử một lớp Java trong dự án Spring Boot và gặp lỗi NullPointerException.

[Mã nguồn]

```java
import java.util.Map;

public class PaymentProcessor {

    private Map<String, Double> exchangeRates;

    public double processPayment(String currency, double amount) {

        double rate = exchangeRates.get(currency);

        return amount * rate;
    }
}
```

[Stack Trace]

```text
Exception in thread "main" java.lang.NullPointerException:
Cannot invoke "java.util.Map.get(Object)"
because "this.exchangeRates" is null

at PaymentProcessor.processPayment(PaymentProcessor.java:7)
at Main.main(Main.java:8)
```

[Nhiệm vụ]

1. Phân tích Stack Trace từng dòng.
2. Xác định chính xác Root Cause của lỗi.
3. Chỉ rõ dòng mã gây lỗi.
4. Giải thích tại sao lỗi xảy ra.
5. Đề xuất cách sửa an toàn nhất.
6. Không được phá vỡ tính bao đóng (Encapsulation).
7. Không được đổi access modifier của thuộc tính exchangeRates thành public.
8. Sử dụng ConcurrentHashMap để khởi tạo dữ liệu.
9. Giải thích vì sao ConcurrentHashMap phù hợp hơn HashMap trong môi trường đa luồng.
10. Sinh ra phiên bản mã nguồn hoàn chỉnh đã sửa.

[Định dạng đầu ra]

* Phân tích Stack Trace
* Root Cause
* Giải pháp sửa lỗi
* Mã nguồn Java hoàn chỉnh
* Giải thích thiết kế OOP được áp dụng

---

## Mã nguồn Java đã sửa hoàn chỉnh

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class PaymentProcessor {

    private final Map<String, Double> exchangeRates;

    public PaymentProcessor() {
        this.exchangeRates = new ConcurrentHashMap<>();
    }

    public void addExchangeRate(String currency, double rate) {
        exchangeRates.put(currency, rate);
    }

    public double processPayment(String currency, double amount) {

        Double rate = exchangeRates.get(currency);

        if (rate == null) {
            throw new IllegalArgumentException(
                    "Exchange rate not found for currency: " + currency);
        }

        return amount * rate;
    }
}
```

## Giải thích lỗi

### 1. Root Cause

Lỗi xảy ra tại dòng:

```java
double rate = exchangeRates.get(currency);
```

Biến:

```java
exchangeRates
```

được khai báo nhưng chưa được khởi tạo:

```java
private Map<String, Double> exchangeRates;
```

Mặc định giá trị của biến tham chiếu là:

```java
null
```

Khi gọi:

```java
exchangeRates.get(currency);
```

thì JVM cố gắng gọi phương thức `get()` trên đối tượng `null`, dẫn đến:

```java
NullPointerException
```

---

### 2. Ý nghĩa Stack Trace

```text
Cannot invoke "java.util.Map.get(Object)"
because "this.exchangeRates" is null
```

Cho biết trực tiếp:

* JVM đang gọi `Map.get()`
* Đối tượng nhận lời gọi là `exchangeRates`
* `exchangeRates == null`

---

```text
at PaymentProcessor.processPayment(PaymentProcessor.java:7)
```

Lỗi phát sinh tại:

```java
double rate = exchangeRates.get(currency);
```

ở dòng 7.

---

```text
at Main.main(Main.java:8)
```

Lớp `Main` gọi `processPayment()` và nhận ngoại lệ từ đó.

---

### 3. Vì sao dùng ConcurrentHashMap?

```java
private final Map<String, Double> exchangeRates =
        new ConcurrentHashMap<>();
```

Ưu điểm:

* Tránh NullPointerException do luôn được khởi tạo.
* An toàn khi nhiều thread cùng truy cập.
* Hiệu năng tốt hơn việc khóa toàn bộ Map như Hashtable.
* Phù hợp với các hệ thống Spring Boot xử lý nhiều request đồng thời.

---

### 4. Đảm bảo Encapsulation

Thuộc tính vẫn là:

```java
private final Map<String, Double> exchangeRates;
```

Không chuyển thành:

```java
public Map<String, Double> exchangeRates;
```

Việc cập nhật dữ liệu được thực hiện thông qua phương thức:

```java
addExchangeRate(...)
```

nên trạng thái nội bộ của đối tượng vẫn được kiểm soát đúng theo nguyên tắc OOP.