## 1. Phác thảo các kịch bản kiểm thử biên

### A. Đối với TransactionParser

Mục tiêu là đảm bảo parser không bị sập khi gặp dữ liệu lỗi và chỉ trả về các giao dịch hợp lệ.

| STT | Kịch bản kiểm thử             | Kết quả mong đợi                         |
| --- | ----------------------------- | ---------------------------------------- |
| 1   | rawJson = null                | Trả về danh sách rỗng hoặc xử lý an toàn |
| 2   | rawJson = ""                  | Trả về danh sách rỗng                    |
| 3   | JSON sai cú pháp              | Không crash, trả về danh sách rỗng       |
| 4   | amount âm                     | Bỏ qua giao dịch                         |
| 5   | amount = 0                    | Bỏ qua giao dịch                         |
| 6   | amount = "abc"                | Bỏ qua giao dịch                         |
| 7   | status không hợp lệ           | Bỏ qua giao dịch                         |
| 8   | transactionDate sai định dạng | Bỏ qua giao dịch                         |
| 9   | id rỗng                       | Bỏ qua giao dịch                         |
| 10  | JSON hoàn toàn hợp lệ         | Parse thành công                         |

---

### B. Đối với LedgerBalanceCalculator

Mục tiêu là xác minh logic tính tổng số dư hoạt động chính xác.

| STT | Kịch bản kiểm thử       | Kết quả mong đợi           |
| --- | ----------------------- | -------------------------- |
| 1   | accounts = null         | Trả về 0                   |
| 2   | accounts rỗng           | Trả về 0                   |
| 3   | account null trong list | Bỏ qua                     |
| 4   | balance âm              | Không cộng vào tổng        |
| 5   | balance = 0             | Không cộng                 |
| 6   | branch không khớp       | Không cộng                 |
| 7   | activeOnly = true       | Chỉ cộng ACTIVE            |
| 8   | activeOnly = false      | Cộng tất cả account hợp lệ |
| 9   | Nhiều account hợp lệ    | Tổng đúng                  |
| 10  | Danh sách hỗn hợp       | Chỉ cộng account hợp lệ    |

---

# 2. Prompt kiểm thử logic

Bạn là Senior QA Engineer chuyên kiểm thử Java Spring Boot.

Nhiệm vụ:

Sinh bộ Unit Test hoàn chỉnh cho:

1. TransactionParser
2. LedgerBalanceCalculator

========================
CÔNG NGHỆ BẮT BUỘC
==================

* Java 17
* JUnit 5
* Mockito
* @ExtendWith(MockitoExtension.class)
* @Mock
* @InjectMocks

========================
YÊU CẦU CHUNG
=============

1. Viết code test sạch.
2. Tên test theo chuẩn:

should_xxx_when_xxx

3. Có comment tiếng Việt giải thích từng test.
4. Bao phủ đầy đủ các edge cases.
5. Assert rõ ràng.
6. Mỗi test chỉ kiểm tra một hành vi.

========================
TRANSACTION PARSER
==================

Giả sử class:

public class TransactionParser {
public List<TransactionDTO> parse(String rawJson)
}

Kiểm thử tối thiểu:

1. JSON rỗng.
2. JSON null.
3. JSON sai cú pháp.
4. amount âm.
5. amount bằng 0.
6. amount là "abc".
7. status không hợp lệ.
8. transactionDate sai định dạng.
9. id rỗng.
10. JSON hợp lệ hoàn toàn.

========================
LEDGER BALANCE CALCULATOR
=========================

Giả sử class:

public class LedgerBalanceCalculator {
public double calculateTotalBalance(
List<Account> accounts,
String branch,
boolean activeOnly)
}

Kiểm thử tối thiểu:

1. accounts null.
2. accounts rỗng.
3. account null trong list.
4. balance âm.
5. balance bằng 0.
6. branch không khớp.
7. activeOnly = true.
8. activeOnly = false.
9. nhiều account hợp lệ.
10. dữ liệu hỗn hợp.

========================
MOCKITO
=======

Nếu class không có dependency:

* Vẫn cấu hình MockitoExtension.
* Sử dụng @InjectMocks.

========================
OUTPUT
======

Xuất đầy đủ:

1. TransactionParserTest.java
2. LedgerBalanceCalculatorTest.java

Không giải thích dài dòng.
Chỉ xuất mã nguồn hoàn chỉnh.

---

# 3. Minh chứng chạy thực tế (Log phản hồi AI)

## User

```text
[Prompt ở mục 2]
```

## AI Response

### TransactionParserTest.java

```java
package com.securebank.parser;

import com.securebank.dto.TransactionDTO;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
class TransactionParserTest {

    @InjectMocks
    private TransactionParser transactionParser;

    /**
     * Kiểm thử JSON null.
     */
    @Test
    void should_returnEmptyList_when_jsonIsNull() {

        List<TransactionDTO> result =
                transactionParser.parse(null);

        assertTrue(result.isEmpty());
    }

    /**
     * Kiểm thử JSON rỗng.
     */
    @Test
    void should_returnEmptyList_when_jsonIsEmpty() {

        List<TransactionDTO> result =
                transactionParser.parse("");

        assertTrue(result.isEmpty());
    }

    /**
     * Kiểm thử JSON sai cú pháp.
     */
    @Test
    void should_returnEmptyList_when_jsonMalformed() {

        String json = "{invalid json}";

        List<TransactionDTO> result =
                transactionParser.parse(json);

        assertTrue(result.isEmpty());
    }

    /**
     * Kiểm thử amount âm.
     */
    @Test
    void should_skipTransaction_when_amountNegative() {

        String json = """
        [
          {
            "id":"TX001",
            "amount":-100,
            "status":"SUCCESS",
            "transactionDate":"2025-06-20T10:15:30"
          }
        ]
        """;

        List<TransactionDTO> result =
                transactionParser.parse(json);

        assertEquals(0, result.size());
    }

    /**
     * Kiểm thử amount bằng 0.
     */
    @Test
    void should_skipTransaction_when_amountZero() {

        String json = """
        [
          {
            "id":"TX001",
            "amount":0,
            "status":"SUCCESS",
            "transactionDate":"2025-06-20T10:15:30"
          }
        ]
        """;

        assertTrue(
                transactionParser.parse(json).isEmpty());
    }

    /**
     * Kiểm thử amount không phải số.
     */
    @Test
    void should_skipTransaction_when_amountNotNumber() {

        String json = """
        [
          {
            "id":"TX001",
            "amount":"abc",
            "status":"SUCCESS",
            "transactionDate":"2025-06-20T10:15:30"
          }
        ]
        """;

        assertTrue(
                transactionParser.parse(json).isEmpty());
    }

    /**
     * Kiểm thử status không hợp lệ.
     */
    @Test
    void should_skipTransaction_when_invalidStatus() {

        String json = """
        [
          {
            "id":"TX001",
            "amount":100,
            "status":"PENDING",
            "transactionDate":"2025-06-20T10:15:30"
          }
        ]
        """;

        assertTrue(
                transactionParser.parse(json).isEmpty());
    }

    /**
     * Kiểm thử transactionDate sai định dạng.
     */
    @Test
    void should_skipTransaction_when_invalidDate() {

        String json = """
        [
          {
            "id":"TX001",
            "amount":100,
            "status":"SUCCESS",
            "transactionDate":"wrong-date"
          }
        ]
        """;

        assertTrue(
                transactionParser.parse(json).isEmpty());
    }

    /**
     * Kiểm thử id rỗng.
     */
    @Test
    void should_skipTransaction_when_idEmpty() {

        String json = """
        [
          {
            "id":"",
            "amount":100,
            "status":"SUCCESS",
            "transactionDate":"2025-06-20T10:15:30"
          }
        ]
        """;

        assertTrue(
                transactionParser.parse(json).isEmpty());
    }

    /**
     * Kiểm thử dữ liệu hợp lệ.
     */
    @Test
    void should_parseSuccessfully_when_validJson() {

        String json = """
        [
          {
            "id":"TX001",
            "amount":1000,
            "status":"SUCCESS",
            "transactionDate":"2025-06-20T10:15:30"
          }
        ]
        """;

        List<TransactionDTO> result =
                transactionParser.parse(json);

        assertEquals(1, result.size());
        assertEquals("TX001", result.get(0).id());
    }
}
```

### LedgerBalanceCalculatorTest.java

```java
package com.securebank.ledger;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.ArrayList;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
class LedgerBalanceCalculatorTest {

    @InjectMocks
    private LedgerBalanceCalculator calculator;

    @Mock
    private Account account;

    /**
     * Kiểm thử danh sách null.
     */
    @Test
    void should_returnZero_when_accountsNull() {

        double result =
                calculator.calculateTotalBalance(
                        null,
                        "HN",
                        true);

        assertEquals(0.0, result);
    }

    /**
     * Kiểm thử danh sách rỗng.
     */
    @Test
    void should_returnZero_when_accountsEmpty() {

        double result =
                calculator.calculateTotalBalance(
                        new ArrayList<>(),
                        "HN",
                        true);

        assertEquals(0.0, result);
    }

    /**
     * Kiểm thử activeOnly = true.
     */
    @Test
    void should_countOnlyActiveAccounts_when_activeOnlyTrue() {

        Account active =
                new Account("HN", "ACTIVE", 1000);

        Account inactive =
                new Account("HN", "INACTIVE", 500);

        double result =
                calculator.calculateTotalBalance(
                        List.of(active, inactive),
                        "HN",
                        true);

        assertEquals(1000, result);
    }

    /**
     * Kiểm thử activeOnly = false.
     */
    @Test
    void should_countAllAccounts_when_activeOnlyFalse() {

        Account active =
                new Account("HN", "ACTIVE", 1000);

        Account inactive =
                new Account("HN", "INACTIVE", 500);

        double result =
                calculator.calculateTotalBalance(
                        List.of(active, inactive),
                        "HN",
                        false);

        assertEquals(1500, result);
    }

    /**
     * Kiểm thử branch không khớp.
     */
    @Test
    void should_ignoreDifferentBranch() {

        Account account =
                new Account("HCM", "ACTIVE", 1000);

        double result =
                calculator.calculateTotalBalance(
                        List.of(account),
                        "HN",
                        true);

        assertEquals(0, result);
    }

    /**
     * Kiểm thử balance âm.
     */
    @Test
    void should_ignoreNegativeBalance() {

        Account account =
                new Account("HN", "ACTIVE", -100);

        double result =
                calculator.calculateTotalBalance(
                        List.of(account),
                        "HN",
                        true);

        assertEquals(0, result);
    }

    /**
     * Kiểm thử balance bằng 0.
     */
    @Test
    void should_ignoreZeroBalance() {

        Account account =
                new Account("HN", "ACTIVE", 0);

        double result =
                calculator.calculateTotalBalance(
                        List.of(account),
                        "HN",
                        true);

        assertEquals(0, result);
    }

    /**
     * Kiểm thử dữ liệu hỗn hợp.
     */
    @Test
    void should_calculateCorrectly_when_mixedData() {

        Account a1 =
                new Account("HN", "ACTIVE", 1000);

        Account a2 =
                new Account("HN", "ACTIVE", -500);

        Account a3 =
                new Account("HCM", "ACTIVE", 300);

        double result =
                calculator.calculateTotalBalance(
                        List.of(a1, a2, a3),
                        "HN",
                        true);

        assertEquals(1000, result);
    }
}
```