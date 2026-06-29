## 1. Phân tích các trường hợp biên khi đọc dữ liệu từ hệ thống Core Banking cũ

Khi nhập dữ liệu JSON từ hệ thống cũ, có thể gặp nhiều lỗi dữ liệu làm ảnh hưởng đến quá trình parse:

### Lỗi cấu trúc JSON

* JSON không đúng cú pháp.
* Thiếu dấu ngoặc `{}` hoặc `[]`.
* Thiếu dấu phẩy giữa các thuộc tính.
* JSON bị cắt giữa chừng do lỗi truyền dữ liệu.

Ví dụ:

```json
[
  {
    "id":"TX001"
    "amount":1000
  }
]
```

---

### Lỗi trường id

* id bị null.
* id là chuỗi rỗng.
* id chỉ chứa khoảng trắng.
* id bị thiếu hoàn toàn.

Ví dụ:

```json
{
  "id":"",
  "amount":1000
}
```

---

### Lỗi trường amount

* amount âm.
* amount bằng 0.
* amount là chuỗi chữ.
* amount chứa ký tự đặc biệt.
* amount bị thiếu.

Ví dụ:

```json
{
  "amount":"abc"
}
```

hoặc

```json
{
  "amount":-500
}
```

---

### Lỗi trường status

Theo yêu cầu chỉ cho phép:

```text
SUCCESS
FAILED
```

Các giá trị lỗi:

```text
PENDING
DONE
ERROR
NULL
""
```

Ví dụ:

```json
{
  "status":"DONE"
}
```

---

### Lỗi trường transactionDate

* Sai định dạng.
* Không đúng chuẩn ISO LocalDateTime.
* Null.
* Chuỗi rỗng.

Ví dụ:

```json
{
  "transactionDate":"31-13-2025"
}
```

---

### Lỗi từng bản ghi nhưng không được làm sập hệ thống

Nếu 1 giao dịch lỗi:

```json
{
  "id":"",
  "amount":"abc",
  "status":"SUCCESS"
}
```

thì:

* Ghi log lỗi.
* Bỏ qua bản ghi đó.
* Tiếp tục xử lý các giao dịch còn lại.

Đây là yêu cầu quan trọng của hệ thống import dữ liệu thực tế.

---

# 2. Prompt tối ưu

Bạn là Senior Java Developer chuyên về Spring Boot và xử lý dữ liệu tích hợp hệ thống.

Nhiệm vụ của bạn là thiết kế và sinh mã nguồn Java 17 hoàn chỉnh cho chức năng import giao dịch từ hệ thống Core Banking cũ.

YÊU CẦU DRY-RUN TRƯỚC KHI SINH CODE

Trước khi viết mã nguồn, hãy:

1. Phân tích dữ liệu đầu vào.
2. Liệt kê các trường hợp biên có thể xảy ra.
3. Trình bày luồng xử lý từng bước.
4. Phác thảo thuật toán (pseudo-code).
5. Sau đó mới sinh code Java.

Không được bỏ qua bước phân tích và thuật toán.

========================
BÀI TOÁN
========

Input:

Một Raw JSON String chứa danh sách giao dịch.

Mỗi giao dịch có:

* id
* amount
* status
* transactionDate

Ví dụ dữ liệu:

[
{
"id":"TX001",
"amount":1000,
"status":"SUCCESS",
"transactionDate":"2025-06-20T10:15:30"
},
{
"id":"",
"amount":"abc",
"status":"INVALID",
"transactionDate":"wrong-date"
}
]

========================
OUTPUT
======

Trả về:

List<TransactionDTO>

Trong đó:

public record TransactionDTO(
String id,
BigDecimal amount,
String status,
LocalDateTime localDateTime
) {}

========================
RÀNG BUỘC KỸ THUẬT
==================

* Java 17
* Spring Boot
* Jackson ObjectMapper
* Sử dụng java.time.LocalDateTime
* Sử dụng BigDecimal cho amount

========================
VALIDATION
==========

Một giao dịch hợp lệ khi:

1. id không null
2. id không rỗng
3. amount là số
4. amount > 0
5. status chỉ được:

    * SUCCESS
    * FAILED
6. transactionDate parse được thành LocalDateTime

Nếu giao dịch không hợp lệ:

* Ghi log lỗi
* Bỏ qua giao dịch đó
* Không được làm dừng toàn bộ tiến trình

========================
LOGGING
=======

Sử dụng SLF4J Logger.

Log phải ghi:

* id giao dịch (nếu có)
* loại lỗi

Không log stacktrace dài dòng trừ khi JSON tổng thể bị lỗi.

========================
MÃ NGUỒN CẦN SINH
=================

1. TransactionDTO.java (record)

2. TransactionParser.java

Yêu cầu TransactionParser:

public class TransactionParser {

```
public List<TransactionDTO> parse(String rawJson)
```

}

========================
TEST DEMO
=========

Tạo thêm đoạn main hoặc ví dụ sử dụng để minh họa:

* dữ liệu hợp lệ
* amount âm
* amount là chuỗi "abc"
* status sai
* transactionDate sai định dạng

Hiển thị kết quả cuối cùng sau khi parse.

Xuất theo thứ tự:

1. Phân tích dữ liệu
2. Danh sách edge cases
3. Thuật toán từng bước
4. Mã nguồn TransactionDTO
5. Mã nguồn TransactionParser
6. Ví dụ chạy thử

---

# 3. Minh chứng chạy thực tế (Log phản hồi AI)

## User

```text
[Prompt ở mục 2]
```

## AI Response

### Bước 1 - Phân tích dữ liệu

```text
Input là chuỗi JSON thô chứa danh sách giao dịch.

Mỗi giao dịch có:
- id
- amount
- status
- transactionDate

Dữ liệu có thể chứa lỗi do hệ thống cũ sinh ra.

Mục tiêu:
- Parse toàn bộ dữ liệu.
- Chỉ giữ lại giao dịch hợp lệ.
- Bỏ qua giao dịch lỗi.
- Không làm gián đoạn tiến trình import.
```

---

### Bước 2 - Edge Cases

```text
1. JSON tổng thể không hợp lệ.

2. id null.

3. id rỗng.

4. amount null.

5. amount là chuỗi chữ.

6. amount <= 0.

7. status không thuộc SUCCESS hoặc FAILED.

8. transactionDate sai định dạng.

9. transactionDate null.

10. Thiếu trường trong JSON.
```

---

### Bước 3 - Thuật toán

```text
Algorithm:

1. Khởi tạo ObjectMapper.

2. Parse rawJson thành JsonNode.

3. Duyệt từng phần tử trong mảng.

4. Đọc:
   - id
   - amount
   - status
   - transactionDate

5. Validate dữ liệu.

6. Nếu dữ liệu không hợp lệ:
   - log lỗi
   - continue

7. Parse LocalDateTime.

8. Tạo TransactionDTO.

9. Thêm vào danh sách kết quả.

10. Trả về List<TransactionDTO>.
```

---

### TransactionDTO.java

```java
package com.securebank.dto;

import java.math.BigDecimal;
import java.time.LocalDateTime;

public record TransactionDTO(
        String id,
        BigDecimal amount,
        String status,
        LocalDateTime localDateTime
) {
}
```

---

### TransactionParser.java

```java
package com.securebank.parser;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.securebank.dto.TransactionDTO;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

public class TransactionParser {

    private static final Logger logger =
            LoggerFactory.getLogger(TransactionParser.class);

    private final ObjectMapper objectMapper =
            new ObjectMapper();

    public List<TransactionDTO> parse(String rawJson) {

        List<TransactionDTO> result = new ArrayList<>();

        try {

            JsonNode root =
                    objectMapper.readTree(rawJson);

            for (JsonNode node : root) {

                try {

                    String id =
                            node.path("id").asText();

                    String amountText =
                            node.path("amount").asText();

                    String status =
                            node.path("status").asText();

                    String dateText =
                            node.path("transactionDate").asText();

                    if (id == null || id.isBlank()) {

                        logger.error(
                                "Invalid transaction: empty id");

                        continue;
                    }

                    BigDecimal amount;

                    try {

                        amount =
                                new BigDecimal(amountText);

                    } catch (Exception ex) {

                        logger.error(
                                "Transaction {} invalid amount",
                                id);

                        continue;
                    }

                    if (amount.compareTo(BigDecimal.ZERO) <= 0) {

                        logger.error(
                                "Transaction {} amount <= 0",
                                id);

                        continue;
                    }

                    if (!"SUCCESS".equals(status)
                            && !"FAILED".equals(status)) {

                        logger.error(
                                "Transaction {} invalid status",
                                id);

                        continue;
                    }

                    LocalDateTime localDateTime;

                    try {

                        localDateTime =
                                LocalDateTime.parse(dateText);

                    } catch (Exception ex) {

                        logger.error(
                                "Transaction {} invalid date",
                                id);

                        continue;
                    }

                    result.add(
                            new TransactionDTO(
                                    id,
                                    amount,
                                    status,
                                    localDateTime));

                } catch (Exception ex) {

                    logger.error(
                            "Skip invalid transaction record");
                }
            }

        } catch (Exception ex) {

            logger.error(
                    "Invalid JSON format",
                    ex);
        }

        return result;
    }
}
```

---

### Ví dụ chạy thử

```java
String rawJson = """
[
  {
    "id":"TX001",
    "amount":1000,
    "status":"SUCCESS",
    "transactionDate":"2025-06-20T10:15:30"
  },
  {
    "id":"",
    "amount":"abc",
    "status":"INVALID",
    "transactionDate":"wrong-date"
  },
  {
    "id":"TX002",
    "amount":-500,
    "status":"FAILED",
    "transactionDate":"2025-06-20T11:00:00"
  }
]
""";

TransactionParser parser =
        new TransactionParser();

List<TransactionDTO> result =
        parser.parse(rawJson);

System.out.println(result);
```

### Kết quả mong đợi

```text
ERROR Invalid transaction: empty id
ERROR Transaction TX002 amount <= 0

[
  TransactionDTO[
      id=TX001,
      amount=1000,
      status=SUCCESS,
      localDateTime=2025-06-20T10:15:30
  ]
]
```