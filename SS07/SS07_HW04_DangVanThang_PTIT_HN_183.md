# Đáp án: B

Phương án **B** là prompt tối ưu nhất vì nó mô tả rõ:

* Vai trò chuyên gia kiểm thử (Senior QA Engineer).
* Công nghệ cần dùng (JUnit 5).
* Kỹ thuật kiểm thử bắt buộc (Parameterized Test).
* Các điều kiện biên cần bao phủ (Edge Cases).
* Định dạng đầu ra mong muốn.

Điều này giúp AI sinh ra bộ test có độ bao phủ cao và giảm nguy cơ bỏ sót các trường hợp logic quan trọng.

---

# Phân tích theo kỹ thuật đặt Prompt kiểm thử logic

## 1. Role (Vai trò)

Phương án B chỉ rõ:

```text
Hãy đóng vai trò là một chuyên gia kiểm thử phần mềm (Senior QA Engineer)
```

Điều này định hướng AI:

* Tập trung vào kiểm thử.
* Ưu tiên Edge Cases.
* Ưu tiên độ bao phủ thay vì chỉ viết test đơn giản.

Trong khi đó A chỉ nói:

```text
Hãy viết unit test...
```

AI có thể chỉ tạo vài test cơ bản.

---

## 2. Testing Strategy

Phương án B yêu cầu rõ:

```text
Sử dụng Parameterized Test
```

Ví dụ:

```java
@ParameterizedTest
@ValueSource(strings = {...})
```

Ưu điểm:

* Giảm mã lặp.
* Dễ mở rộng.
* Chuẩn JUnit 5.

A không yêu cầu điều này.

---

## 3. Edge Case Coverage

B chỉ rõ các trường hợp biên:

### Null

```java
null
```

### Empty

```java
""
```

### Chứa khoảng trắng

```java
"Password 1@"
```

### Quá ngắn

```java
"Abc1@xy"
```

(7 ký tự)

### Quá dài

```java
"Abcdefghijklmnopqrs1@X"
```

(21 ký tự)

### Thiếu chữ hoa

```java
"password1@"
```

### Thiếu chữ thường

```java
"PASSWORD1@"
```

### Thiếu số

```java
"Password@"
```

### Thiếu ký tự đặc biệt

```java
"Password1"
```

Đây chính là kiểm thử logic và boundary testing.

---

## 4. Output Specification

Prompt B yêu cầu:

```text
Mã nguồn Java JUnit 5 hoàn chỉnh
```

và

```text
Giải thích tiếng Việt
```

AI ít có khả năng trả lời thiếu hoặc sai định dạng.

---

# Vì sao loại A?

Prompt A:

```text
Hãy viết unit test cho class PasswordValidator của tôi, test các trường hợp mật khẩu hợp lệ và không hợp lệ.
```

Nhược điểm:

### Quá mơ hồ

Không chỉ rõ:

* JUnit phiên bản nào.
* Có dùng Parameterized Test không.
* Các trường hợp biên nào cần test.

AI dễ sinh:

```java
testValidPassword()
testInvalidPassword()
```

rồi dừng lại.

### Độ bao phủ thấp

Có thể bỏ sót:

* null
* chuỗi rỗng
* 7 ký tự
* 21 ký tự
* khoảng trắng

=> Không đáp ứng yêu cầu kiểm thử logic.

---

# Vì sao loại C?

Prompt C:

```text
Hãy viết lại hàm PasswordValidator sử dụng regex ngắn hơn để tôi không phải viết unit test.
```

Sai hoàn toàn mục tiêu.

Mục tiêu đề bài:

```text
Viết unit test
```

C lại:

```text
Refactor code
```

Ngoài ra:

* Regex ngắn hơn không chứng minh logic đúng.
* Unit Test vẫn bắt buộc.
* Không hỗ trợ kiểm thử hồi quy (Regression Testing).

=> Không phù hợp.

---

# Mã nguồn JUnit 5 Test Class

Giả sử PasswordValidator có phương thức:

```java
boolean isValid(String password)
```

thì bộ test hoàn chỉnh:

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.NullAndEmptySource;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

class PasswordValidatorTest {

    private final PasswordValidator validator =
            new PasswordValidator();

    @ParameterizedTest
    @DisplayName("Valid passwords")
    @ValueSource(strings = {
            "Password1@",
            "Abc1234#",
            "StrongPass9$",
            "Java2025%"
    })
    void shouldReturnTrueForValidPasswords(String password) {

        assertTrue(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Null and empty passwords")
    @NullAndEmptySource
    void shouldReturnFalseForNullOrEmptyPasswords(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Password too short")
    @ValueSource(strings = {
            "Ab1@xyz"
    })
    void shouldReturnFalseForShortPasswords(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Password too long")
    @ValueSource(strings = {
            "Abcdefghijklmnopqrs1@X"
    })
    void shouldReturnFalseForLongPasswords(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Missing uppercase letter")
    @ValueSource(strings = {
            "password1@",
            "java2025#"
    })
    void shouldReturnFalseWhenMissingUppercase(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Missing lowercase letter")
    @ValueSource(strings = {
            "PASSWORD1@",
            "JAVA2025#"
    })
    void shouldReturnFalseWhenMissingLowercase(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Missing digit")
    @ValueSource(strings = {
            "Password@",
            "JavaTest#"
    })
    void shouldReturnFalseWhenMissingDigit(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Missing special character")
    @ValueSource(strings = {
            "Password1",
            "Java2025"
    })
    void shouldReturnFalseWhenMissingSpecialCharacter(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @ParameterizedTest
    @DisplayName("Password contains spaces")
    @ValueSource(strings = {
            "Password 1@",
            " Java2025#",
            "Password1@ "
    })
    void shouldReturnFalseWhenContainingSpaces(
            String password
    ) {

        assertFalse(
                validator.isValid(password)
        );
    }

    @Test
    @DisplayName("Boundary test - exactly 8 characters")
    void shouldAcceptPasswordWithLengthEight() {

        assertTrue(
                validator.isValid("Abc12@de")
        );
    }

    @Test
    @DisplayName("Boundary test - exactly 20 characters")
    void shouldAcceptPasswordWithLengthTwenty() {

        assertTrue(
                validator.isValid("Abcdefghijklmno1@Pq")
        );
    }
}
```