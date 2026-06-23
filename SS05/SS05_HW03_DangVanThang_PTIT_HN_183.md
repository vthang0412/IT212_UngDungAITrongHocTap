# 1. Phân tích các lỗ hổng nghiêm trọng của mã nguồn ban đầu

## 1.1. Không kiểm tra null

Đoạn code:

```java
Account from = accountRepository.findById(fromAccountId).orElse(null);
Account to = accountRepository.findById(toAccountId).orElse(null);
```

Nếu tài khoản không tồn tại:

```java
from.setBalance(...)
```

sẽ gây:

```java
NullPointerException
```

Hậu quả:

* Hệ thống lỗi Runtime.
* Người dùng không nhận được thông báo rõ ràng.
* Khó xác định nguyên nhân.

---

## 1.2. Không kiểm tra số tiền chuyển

Hiện tại cho phép:

```java
transfer(1L, 2L, -1000);
```

Kết quả:

```java
from.balance = from.balance + 1000
to.balance   = to.balance - 1000
```

Tiền bị đảo chiều trái phép.

---

## 1.3. Không kiểm tra số dư khả dụng

Ví dụ:

```java
Số dư = 1.000.000
Chuyển = 10.000.000
```

Code vẫn thực hiện:

```java
from.balance = -9.000.000
```

Vi phạm nghiệp vụ ngân hàng.

---

## 1.4. Không có Transaction

Giả sử:

```java
save(from);
```

thành công.

Nhưng:

```java
save(to);
```

bị lỗi DB.

Kết quả:

```text
Tài khoản A bị trừ tiền
Tài khoản B không nhận tiền
```

Tiền "biến mất".

Đây là lỗi cực kỳ nghiêm trọng.

---

## 1.5. Không đảm bảo ACID

Thiếu:

```java
@Transactional
```

=> Không đảm bảo:

* Atomicity
* Consistency
* Isolation
* Durability

---

## 1.6. Không xử lý Exception

Không có:

```java
try-catch
```

hoặc:

```java
Custom Exception
```

=> Exception bị đẩy thẳng lên trên.

---

## 1.7. Không có Logging

Không log:

* Ai chuyển tiền
* Chuyển bao nhiêu
* Thành công hay thất bại

Khó audit và điều tra sự cố.

---

## 1.8. Dùng double cho tiền tệ

```java
double amount
```

Lỗi:

```java
0.1 + 0.2 != 0.3
```

Trong tài chính phải dùng:

```java
BigDecimal
```

---

# 2. Chuỗi Prompt cải tiến

## Vòng 1 - Robustness

Bạn là Senior Java Developer.

Hãy refactor đoạn code chuyển tiền sau theo hướng tăng độ Robustness.

Yêu cầu:

1. Kiểm tra tài khoản nguồn và đích có tồn tại hay không.
2. Kiểm tra số tiền chuyển phải lớn hơn 0.
3. Kiểm tra tài khoản nguồn có đủ số dư.
4. Không sử dụng NullPointerException.
5. Tạo các Custom Exception phù hợp:

    * AccountNotFoundException
    * InvalidAmountException
    * InsufficientBalanceException
6. Sử dụng BigDecimal thay cho double.
7. Giải thích từng thay đổi trước khi sinh code.

## Vòng 2 - Maintainability / Clean Code

Tiếp tục cải tiến đoạn code ở vòng trước.

Yêu cầu:

1. Sử dụng Spring Boot.
2. Thêm @Transactional để đảm bảo Atomicity.
3. Sử dụng Lombok:

    * @RequiredArgsConstructor
    * @Slf4j
4. Log các sự kiện:

    * Bắt đầu chuyển tiền
    * Chuyển tiền thành công
    * Chuyển tiền thất bại
5. Áp dụng Clean Code:

    * Tách method validation
    * Đặt tên biến rõ nghĩa
    * Giảm lặp code
6. Giải thích vì sao @Transactional giải quyết vấn đề mất tiền khi lỗi xảy ra.

## Vòng 3 - Context-specific Tuning

Tiếp tục cải tiến phiên bản hiện tại.

Yêu cầu:

1. Trả về TransactionResult thay vì void.
2. TransactionResult gồm:

    * success
    * transactionId
    * message
    * transferAmount
3. Viết JUnit 5 + Mockito test.
4. Viết test cho trường hợp:

    * Không đủ số dư
5. Verify:

    * Exception được ném ra
    * accountRepository.save() không được gọi
6. Sinh mã nguồn hoàn chỉnh.

# 3. Mã nguồn Java đã tối ưu

```java
package com.bank.service;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.math.BigDecimal;
import java.util.UUID;

@Service
@RequiredArgsConstructor
@Slf4j
public class AccountService {

    private final AccountRepository accountRepository;

    @Transactional
    public TransactionResult transfer(
            Long fromAccountId,
            Long toAccountId,
            BigDecimal amount) {

        log.info("Transfer started: from={}, to={}, amount={}",
                fromAccountId, toAccountId, amount);

        validateAmount(amount);

        Account fromAccount = accountRepository.findById(fromAccountId)
                .orElseThrow(() ->
                        new AccountNotFoundException(
                                "Source account not found"));

        Account toAccount = accountRepository.findById(toAccountId)
                .orElseThrow(() ->
                        new AccountNotFoundException(
                                "Destination account not found"));

        validateBalance(fromAccount, amount);

        fromAccount.setBalance(
                fromAccount.getBalance().subtract(amount));

        toAccount.setBalance(
                toAccount.getBalance().add(amount));

        accountRepository.save(fromAccount);
        accountRepository.save(toAccount);

        String transactionId = UUID.randomUUID().toString();

        log.info("Transfer successful: txId={}", transactionId);

        return new TransactionResult(
                true,
                transactionId,
                "Transfer completed successfully",
                amount
        );
    }

    private void validateAmount(BigDecimal amount) {

        if (amount == null ||
            amount.compareTo(BigDecimal.ZERO) <= 0) {

            throw new InvalidAmountException(
                    "Amount must be greater than zero");
        }
    }

    private void validateBalance(
            Account account,
            BigDecimal amount) {

        if (account.getBalance().compareTo(amount) < 0) {

            throw new InsufficientBalanceException(
                    "Insufficient balance");
        }
    }
}
```

```java
package com.bank.dto;

import java.math.BigDecimal;

public record TransactionResult(
        boolean success,
        String transactionId,
        String message,
        BigDecimal transferAmount
) {
}
```

```java
package com.bank.exception;

public class AccountNotFoundException
        extends RuntimeException {

    public AccountNotFoundException(String message) {
        super(message);
    }
}
```

```java
package com.bank.exception;

public class InvalidAmountException
        extends RuntimeException {

    public InvalidAmountException(String message) {
        super(message);
    }
}
```

```java
package com.bank.exception;

public class InsufficientBalanceException
        extends RuntimeException {

    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

# 4. JUnit Test

```java
package com.bank.service;

import com.bank.exception.InsufficientBalanceException;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.math.BigDecimal;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class AccountServiceTest {

    @Mock
    private AccountRepository accountRepository;

    @InjectMocks
    private AccountService accountService;

    @Test
    void transfer_shouldThrowException_whenBalanceNotEnough() {

        Account from = new Account();
        from.setId(1L);
        from.setBalance(new BigDecimal("1000"));

        Account to = new Account();
        to.setId(2L);
        to.setBalance(new BigDecimal("500"));

        when(accountRepository.findById(1L))
                .thenReturn(Optional.of(from));

        when(accountRepository.findById(2L))
                .thenReturn(Optional.of(to));

        assertThrows(
                InsufficientBalanceException.class,
                () -> accountService.transfer(
                        1L,
                        2L,
                        new BigDecimal("5000"))
        );

        verify(accountRepository, never())
                .save(any(Account.class));
    }
}
```