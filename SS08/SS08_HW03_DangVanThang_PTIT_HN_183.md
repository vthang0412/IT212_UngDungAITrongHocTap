## 1. Phân tích các điểm vi phạm Clean Code của mã nguồn ban đầu

### 1. Tên biến không có ý nghĩa

```java
double total = 0;
for (Account a : list)
```

Các tên:

* `a`
* `list`
* `total`
* `calc`

quá ngắn và thiếu ngữ nghĩa nghiệp vụ.

Nên đổi thành:

```java
accounts
account
totalBalance
calculateTotalBalance
```

---

### 2. If-else lồng nhau quá sâu (Arrow Code)

Code hiện tại có tới 5 mức lồng nhau:

```java
if (list != null) {
    for (...) {
        if (...) {
            if (...) {
                if (...) {
                    if (...) {
                    }
                }
            }
        }
    }
}
```

Vấn đề:

* Khó đọc.
* Khó bảo trì.
* Khó debug.
* Dễ phát sinh lỗi khi mở rộng nghiệp vụ.

---

### 3. Thiếu Guard Clauses

Thay vì:

```java
if (list != null) {
   ...
}
```

nên:

```java
if (accounts == null || accounts.isEmpty()) {
    return 0;
}
```

Giúp luồng xử lý rõ ràng hơn.

---

### 4. Hard-code String

```java
a.getStatus().equals("ACTIVE")
```

Nên sử dụng:

```java
private static final String ACTIVE = "ACTIVE";
```

hoặc Enum.

---

### 5. Dễ phát sinh NullPointerException

```java
a.getBranch().equals(branch)
```

Nếu:

```java
a.getBranch() == null
```

sẽ lỗi.

Nên dùng:

```java
Objects.equals(account.getBranch(), branch)
```

---

### 6. Không có Logging

Khi tính tổng số dư:

* Không biết đã duyệt bao nhiêu tài khoản.
* Không biết tổng tiền tính được.
* Không hỗ trợ audit.

---

### 7. Chưa tận dụng Java 17

Code đang viết theo phong cách Java 5.

Có thể thay bằng:

* Stream API
* Predicate
* Method Reference

giúp ngắn gọn và dễ bảo trì hơn.

---

# 2. Chuỗi Prompt Refinement Chain

## Vòng 1 – Robustness & Clean Code

Bạn là Senior Java Code Reviewer.

Hãy refactor đoạn code Java sau theo nguyên tắc Clean Code.

Mục tiêu:

1. Loại bỏ toàn bộ if-else lồng nhau sâu.
2. Áp dụng Guard Clauses hoặc Return Early.
3. Đổi tên:

   * calc -> calculateTotalBalance
   * list -> accounts
   * a -> account
   * total -> totalBalance
4. Giảm độ phức tạp Cyclomatic Complexity.
5. Không thay đổi nghiệp vụ hiện tại.
6. Tránh NullPointerException.
7. Giải thích các thay đổi đã thực hiện.

```java
import java.util.List;

 

public class LedgerBalanceCalculator {

    public double calc(List<Account> list, String branch, boolean activeOnly) {

        double total = 0;

        if (list != null) {

            for (Account a : list) {

                if (a != null) {

                    if (a.getBranch().equals(branch)) {

                        if (activeOnly) {

                            if (a.getStatus().equals("ACTIVE")) {

                                if (a.getBalance() > 0) {

                                    total += a.getBalance();

                                }

                            }

                        } else {

                            if (a.getBalance() > 0) {

                                total += a.getBalance();

                            }

                        }

                    }

                }

            }

        }

        return total;

    }

}
```

---

## Vòng 2 – Maintainability & OOP

Tiếp tục từ phiên bản đã refactor ở vòng trước.

Yêu cầu:

1. Chuyển toàn bộ vòng lặp sang Java 17 Stream API.
2. Loại bỏ các if kiểm tra thủ công nếu có thể.
3. Tăng khả năng đọc hiểu.
4. Nếu chạy trong môi trường Spring Boot:

   * thêm @Transactional(readOnly = true)
5. Tách các điều kiện filter thành luồng xử lý rõ ràng.
6. Giữ nguyên kết quả nghiệp vụ.
7. Giải thích lợi ích của phiên bản mới.

Chỉ xuất code hoàn chỉnh sau khi tối ưu.

---

## Vòng 3 – Logging & Context Tuning

Tiếp tục từ phiên bản đã tối ưu ở vòng 2.

Yêu cầu bổ sung:

1. Tích hợp Lombok @Slf4j.
2. Log ở mức INFO:

   * số lượng tài khoản đầu vào
   * số lượng tài khoản hợp lệ
   * tổng số dư cuối cùng
3. Logging phải có ý nghĩa nghiệp vụ.
4. Giữ chuẩn Java 17.
5. Giữ @Transactional(readOnly = true).
6. Không thay đổi kết quả tính toán.
7. Xuất duy nhất mã nguồn LedgerBalanceCalculator hoàn chỉnh sau khi tối ưu toàn bộ.

Đây là phiên bản production-ready.

---

# 3. Minh chứng chạy thực tế (Log phản hồi AI ở vòng cuối)

## User

```text
[Prompt vòng 3]
```

## AI Response

### LedgerBalanceCalculator.java

```java
package com.securebank.ledger;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Objects;

@Slf4j
@Service
@Transactional(readOnly = true)
public class LedgerBalanceCalculator {

    private static final String ACTIVE_STATUS = "ACTIVE";

    public double calculateTotalBalance(
            List<Account> accounts,
            String branch,
            boolean activeOnly) {

        if (accounts == null || accounts.isEmpty()) {

            log.info(
                    "No accounts available for branch={}",
                    branch);

            return 0.0;
        }

        long validAccountCount =
                accounts.stream()
                        .filter(Objects::nonNull)
                        .filter(account ->
                                Objects.equals(
                                        account.getBranch(),
                                        branch))
                        .filter(account ->
                                !activeOnly ||
                                ACTIVE_STATUS.equals(
                                        account.getStatus()))
                        .filter(account ->
                                account.getBalance() > 0)
                        .count();

        double totalBalance =
                accounts.stream()
                        .filter(Objects::nonNull)
                        .filter(account ->
                                Objects.equals(
                                        account.getBranch(),
                                        branch))
                        .filter(account ->
                                !activeOnly ||
                                ACTIVE_STATUS.equals(
                                        account.getStatus()))
                        .filter(account ->
                                account.getBalance() > 0)
                        .mapToDouble(Account::getBalance)
                        .sum();

        log.info(
                "Ledger calculation completed. branch={}, totalAccounts={}, validAccounts={}, totalBalance={}",
                branch,
                accounts.size(),
                validAccountCount,
                totalBalance);

        return totalBalance;
    }
}
```