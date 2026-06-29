## 1. Mô tả ngắn gọn ý đồ thiết kế

Mục tiêu của hệ thống đối soát (Reconciliation System) là phát hiện sự khác biệt giữa dữ liệu giao dịch từ Core Banking và dữ liệu từ Visa/Mastercard.

Quy trình thiết kế gồm 3 bước:

* **Bước 1:** So sánh các kiến trúc đối soát để lựa chọn giải pháp phù hợp với quy mô lên tới 5 triệu giao dịch/ngày.
* **Bước 2:** Phân tích rủi ro lệch múi giờ giữa các hệ thống và chuẩn hóa thời gian bằng Java Time API.
* **Bước 3:** Sinh mã nguồn Java Spring Boot hoàn chỉnh cho dịch vụ đối soát có khả năng:

  * Chuẩn hóa thời gian UTC.
  * Đối chiếu theo Transaction ID.
  * Phát hiện lệch số tiền.
  * Phát hiện lệch trạng thái.
  * Ghi log kiểm toán.
  * Tạo bản ghi lỗi hoặc ném ngoại lệ khi phát hiện sai lệch.

---

# 2. Prompt Bước 1 - Options & Trade-offs

Bạn là Senior Solution Architect chuyên thiết kế hệ thống tài chính ngân hàng.

Bối cảnh:

SecureBank cần xây dựng hệ thống đối soát giao dịch giữa:

* Core Banking
* Visa/Mastercard

Số lượng giao dịch:

5 triệu giao dịch/ngày.

Hãy đề xuất và so sánh 2 phương án:

PHƯƠNG ÁN 1

Đối soát trong Java:

* Load toàn bộ dữ liệu Core Banking vào Collection.
* Load toàn bộ dữ liệu Visa vào Collection.
* Sử dụng Map hoặc HashMap để đối chiếu.

PHƯƠNG ÁN 2

Đối soát trực tiếp trong Database:

* Import dữ liệu vào bảng staging.
* Thực hiện SQL JOIN để đối chiếu.

Yêu cầu:

1. Mô tả kiến trúc từng phương án.
2. Phân tích độ phức tạp.
3. So sánh:

  * Hiệu năng
  * RAM
  * CPU
  * Network
  * Khả năng mở rộng
4. Đánh giá khi xử lý:

  * 100.000 giao dịch/ngày
  * 1 triệu giao dịch/ngày
  * 5 triệu giao dịch/ngày
5. Kết luận nên chọn phương án nào cho SecureBank.

Xuất kết quả dưới dạng bảng so sánh chi tiết.

---

# 3. Prompt Bước 2 - What-if Timezone

Bạn là Senior Java Time API Consultant.

What-if Scenario:

Core Banking lưu thời gian giao dịch theo GMT+7.

Ví dụ:

2025-06-20T10:00:00+07:00

Visa lưu thời gian theo GMT+0.

Ví dụ:

2025-06-20T03:00:00Z

Thực tế đây là cùng một thời điểm nhưng hệ thống đối soát báo sai lệch.

Hãy phân tích:

1. Nguyên nhân xảy ra lỗi.
2. Điều gì sẽ xảy ra nếu chỉ so sánh LocalDateTime.
3. Cách xử lý đúng bằng:

  * ZoneId
  * ZonedDateTime
  * Instant
4. Chuẩn hóa toàn bộ giao dịch về UTC trước khi đối soát.
5. Minh họa bằng code Java 17.
6. Liệt kê các rủi ro nếu không chuẩn hóa timezone.

Xuất:

* Phân tích
* Thuật toán
* Ví dụ code
* Best Practices

---

# 4. Prompt Bước 3 - Code Generation with CoT

Bạn là Senior Java Spring Boot Developer.

Trước khi viết code hãy thực hiện Chain of Thought:

1. Phân tích bài toán.
2. Liệt kê các bước đối soát.
3. Thiết kế thuật toán.
4. Sau đó mới sinh code.

========================
YÊU CẦU HỆ THỐNG
================

Xây dựng:

ReconciliationService

public class ReconciliationService {

```
public void reconcile(
        List<Transaction> coreTxns,
        List<Transaction> partnerTxns)
```

}

========================
QUY TẮC ĐỐI SOÁT
================

1. Chuẩn hóa toàn bộ thời gian về UTC.

2. So khớp theo transactionId.

3. Nếu không tìm thấy giao dịch đối ứng:

  * tạo lỗi đối soát.

4. Nếu amount khác:

  * ghi log lỗi.
  * tạo DiscrepancyRecord.

5. Nếu status khác:

  * ghi log lỗi.
  * tạo DiscrepancyRecord.

6. Sử dụng Lombok @Slf4j.

7. Java 17.

8. Spring Boot.

========================
YÊU CẦU CODE
============

Sinh đầy đủ:

* Transaction.java
* DiscrepancyRecord.java
* ReconciliationException.java
* ReconciliationService.java

Sử dụng:

* Stream API
* Map
* ZonedDateTime
* Instant
* BigDecimal

Xuất theo thứ tự:

1. Phân tích
2. Thuật toán
3. Mã nguồn hoàn chỉnh

---

# 5. Minh chứng chạy thực tế (Log chat)

## Lượt Chat 1 - So sánh phương án đối soát

### User

```text
[Prompt Bước 1]
```

### AI Response

#### Bảng so sánh

| Tiêu chí          | Java Collection    | SQL JOIN        |
| ----------------- | ------------------ | --------------- |
| Dữ liệu lưu       | JVM Heap           | Database        |
| RAM tiêu thụ      | Rất cao            | Thấp            |
| CPU ứng dụng      | Cao                | Thấp            |
| CPU Database      | Thấp               | Cao             |
| Độ phức tạp       | O(n) với HashMap   | Phụ thuộc Index |
| Khả năng mở rộng  | Trung bình         | Cao             |
| 100.000 giao dịch | Tốt                | Tốt             |
| 1 triệu giao dịch | Chấp nhận được     | Tốt             |
| 5 triệu giao dịch | Rủi ro OutOfMemory | Tốt hơn         |

#### Kết luận

```text
Với 5 triệu giao dịch/ngày:

Nên chọn SQL JOIN.

Lý do:

- Database tối ưu index.
- Không tiêu tốn heap JVM lớn.
- Dễ scale theo chiều ngang.
- Giảm nguy cơ OutOfMemoryError.
```

---

# Lượt Chat 2 - What-if Timezone

### User

```text
[Prompt Bước 2]
```

### AI Response

#### Phân tích

```text
2025-06-20T10:00:00+07:00

và

2025-06-20T03:00:00Z

là cùng một thời điểm.

Nếu dùng LocalDateTime:

10:00 != 03:00

=> Hệ thống kết luận sai lệch.
```

#### Thuật toán

```text
1. Parse thời gian thành ZonedDateTime.
2. Chuyển sang Instant.
3. Chuyển toàn bộ về UTC.
4. Đối chiếu bằng Instant.
```

#### Ví dụ Code

```java
ZonedDateTime coreTime =
        ZonedDateTime.parse(
                "2025-06-20T10:00:00+07:00");

ZonedDateTime visaTime =
        ZonedDateTime.parse(
                "2025-06-20T03:00:00Z");

Instant coreUtc =
        coreTime.toInstant();

Instant visaUtc =
        visaTime.toInstant();

System.out.println(
        coreUtc.equals(visaUtc));

true
```

#### Best Practice

```text
Không lưu LocalDateTime cho hệ thống đa múi giờ.

Ưu tiên:

- Instant
hoặc
- UTC Timestamp

cho toàn bộ nghiệp vụ đối soát.
```

---

# Lượt Chat 3 - Sinh mã nguồn ReconciliationService

### User

```text
[Prompt Bước 3]
```

### AI Response

### Phân tích

```text
1. Chuẩn hóa thời gian.
2. Lập Map transactionId.
3. Đối chiếu từng giao dịch.
4. So sánh amount.
5. So sánh status.
6. Ghi log.
7. Tạo DiscrepancyRecord.
```

### Thuật toán

```text
Map<String, Transaction> partnerMap

for each coreTransaction:

    tìm partner transaction

    nếu không tồn tại:
        tạo discrepancy

    nếu amount khác:
        tạo discrepancy

    nếu status khác:
        tạo discrepancy
```

### Transaction.java

```java
import java.math.BigDecimal;
import java.time.ZonedDateTime;

public record Transaction(
        String transactionId,
        BigDecimal amount,
        String status,
        ZonedDateTime transactionTime) {
}
```

### DiscrepancyRecord.java

```java
public record DiscrepancyRecord(
        String transactionId,
        String reason) {
}
```

### ReconciliationException.java

```java
public class ReconciliationException
        extends RuntimeException {

    public ReconciliationException(
            String message) {
        super(message);
    }
}
```

### ReconciliationService.java

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;

import java.math.BigDecimal;
import java.time.Instant;
import java.util.List;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

@Slf4j
@Service
public class ReconciliationService {

    public void reconcile(
            List<Transaction> coreTxns,
            List<Transaction> partnerTxns) {

        Map<String, Transaction> partnerMap =
                partnerTxns.stream()
                        .collect(Collectors.toMap(
                                Transaction::transactionId,
                                Function.identity()));

        for (Transaction coreTxn : coreTxns) {

            Instant coreUtc =
                    coreTxn.transactionTime()
                            .toInstant();

            Transaction partnerTxn =
                    partnerMap.get(
                            coreTxn.transactionId());

            if (partnerTxn == null) {

                log.error(
                        "Missing transaction {}",
                        coreTxn.transactionId());

                throw new ReconciliationException(
                        "Missing transaction: "
                                + coreTxn.transactionId());
            }

            Instant partnerUtc =
                    partnerTxn.transactionTime()
                            .toInstant();

            if (!coreUtc.equals(partnerUtc)) {

                log.error(
                        "Timezone mismatch {}",
                        coreTxn.transactionId());
            }

            if (coreTxn.amount()
                    .compareTo(
                            partnerTxn.amount()) != 0) {

                log.error(
                        "Amount mismatch {}",
                        coreTxn.transactionId());
            }

            if (!coreTxn.status()
                    .equals(
                            partnerTxn.status())) {

                log.error(
                        "Status mismatch {}",
                        coreTxn.transactionId());
            }
        }
    }
}
```