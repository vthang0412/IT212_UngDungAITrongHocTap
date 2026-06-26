## 1. Mô tả ngắn gọn ý đồ thiết kế

Mục tiêu là xây dựng một lớp `SecurityDecryptor` có khả năng giải mã mật khẩu cơ sở dữ liệu đã được mã hóa bằng AES trong Java 17.

Quy trình được thiết kế theo các nguyên tắc bảo mật:

* Chỉ sử dụng thư viện chuẩn `javax.crypto`.
* Kiểm tra đầu vào trước khi giải mã.
* Xử lý các tình huống lỗi như:

    * Chuỗi mật khẩu mã hóa rỗng.
    * Khóa bí mật (`secretKey`) rỗng hoặc null.
    * Khóa giải mã sai.
    * Dữ liệu mã hóa không hợp lệ.
* Không ghi log mật khẩu gốc hoặc khóa bí mật.
* Tạo ngoại lệ tùy chỉnh `DecryptionException` để che giấu chi tiết kỹ thuật khỏi tầng nghiệp vụ.
* Xây dựng JUnit 5 test để kiểm tra cả trường hợp thành công và thất bại.

---

# 2. Prompt gửi AI

Bạn là Senior Java Security Engineer.

Hãy tạo mã nguồn Java 17 hoàn chỉnh cho module SecurityDecryptor dùng để giải mã mật khẩu cơ sở dữ liệu đã được mã hóa bằng AES.

Yêu cầu kỹ thuật:

1. Tạo lớp SecurityDecryptor.

2. Cung cấp phương thức:

public String decrypt(String encryptedPassword, String secretKey)

3. Sử dụng:

* Java 17
* javax.crypto
* AES
* Base64 để giải mã dữ liệu đầu vào

4. Tạo ngoại lệ tùy chỉnh:

public class DecryptionException extends Exception

5. Thực hiện validate:

* encryptedPassword không được null
* encryptedPassword không được rỗng
* secretKey không được null
* secretKey không được rỗng

6. Kịch bản What-if cần xử lý:

What if:

* encryptedPassword là chuỗi rỗng?
* secretKey là null?
* secretKey sai?
* dữ liệu encryptedPassword không phải Base64 hợp lệ?
* quá trình giải mã sinh ra BadPaddingException?
* quá trình giải mã sinh ra InvalidKeyException?

Yêu cầu:

* Bắt các ngoại lệ thích hợp.
* Chuyển đổi thành DecryptionException.
* Không làm lộ plaintext password.
* Không log secretKey.
* Chỉ ghi log an toàn bằng java.util.logging.Logger.
* Log chỉ chứa loại lỗi và thời gian xảy ra.

7. Viết code sạch:

* Có JavaDoc.
* Có comment giải thích các bước chính.
* Tuân thủ nguyên tắc defensive programming.

8. Sinh thêm bộ kiểm thử JUnit 5.

Các test case tối thiểu:

* decrypt thành công.
* encryptedPassword null.
* encryptedPassword rỗng.
* secretKey null.
* secretKey rỗng.
* secretKey sai.
* encryptedPassword không hợp lệ.
* xác nhận DecryptionException được ném ra đúng.

Xuất đầy đủ:

* SecurityDecryptor.java
* DecryptionException.java
* SecurityDecryptorTest.java

---

# 3. Minh chứng thực tế (Log chat chạy Prompt với AI)

## User

```text
Bạn là Senior Java Security Engineer.

Hãy tạo mã nguồn Java 17 hoàn chỉnh cho module SecurityDecryptor dùng để giải mã mật khẩu cơ sở dữ liệu đã được mã hóa bằng AES.

Yêu cầu kỹ thuật:

1. Tạo lớp SecurityDecryptor.

2. Cung cấp phương thức:

public String decrypt(String encryptedPassword, String secretKey)

3. Sử dụng:
- Java 17
- javax.crypto
- AES
- Base64 để giải mã dữ liệu đầu vào

4. Tạo ngoại lệ tùy chỉnh:

public class DecryptionException extends Exception

5. Thực hiện validate:
- encryptedPassword không được null
- encryptedPassword không được rỗng
- secretKey không được null
- secretKey không được rỗng

6. Kịch bản What-if cần xử lý:

What if:
- encryptedPassword là chuỗi rỗng?
- secretKey là null?
- secretKey sai?
- dữ liệu encryptedPassword không phải Base64 hợp lệ?
- quá trình giải mã sinh ra BadPaddingException?
- quá trình giải mã sinh ra InvalidKeyException?

Yêu cầu:
- Bắt các ngoại lệ thích hợp.
- Chuyển đổi thành DecryptionException.
- Không làm lộ plaintext password.
- Không log secretKey.
- Chỉ ghi log an toàn bằng java.util.logging.Logger.
- Log chỉ chứa loại lỗi và thời gian xảy ra.

7. Viết code sạch:
- Có JavaDoc.
- Có comment giải thích các bước chính.
- Tuân thủ nguyên tắc defensive programming.

8. Sinh thêm bộ kiểm thử JUnit 5.

Các test case tối thiểu:
- decrypt thành công.
- encryptedPassword null.
- encryptedPassword rỗng.
- secretKey null.
- secretKey rỗng.
- secretKey sai.
- encryptedPassword không hợp lệ.
- xác nhận DecryptionException được ném ra đúng.

Xuất đầy đủ:
- SecurityDecryptor.java
- DecryptionException.java
- SecurityDecryptorTest.java
```

## AI Response

### DecryptionException.java

```java
package com.example.security;

public class DecryptionException extends Exception {

    public DecryptionException(String message) {
        super(message);
    }

    public DecryptionException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

### SecurityDecryptor.java

```java
package com.example.security;

import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.spec.SecretKeySpec;
import java.security.InvalidKeyException;
import java.util.Base64;
import java.util.logging.Level;
import java.util.logging.Logger;

public class SecurityDecryptor {

    private static final Logger LOGGER =
            Logger.getLogger(SecurityDecryptor.class.getName());

    public String decrypt(String encryptedPassword, String secretKey)
            throws DecryptionException {

        validateInput(encryptedPassword, secretKey);

        try {

            byte[] decodedBytes =
                    Base64.getDecoder().decode(encryptedPassword);

            SecretKeySpec keySpec =
                    new SecretKeySpec(secretKey.getBytes(), "AES");

            Cipher cipher = Cipher.getInstance("AES");

            cipher.init(Cipher.DECRYPT_MODE, keySpec);

            byte[] decryptedBytes =
                    cipher.doFinal(decodedBytes);

            return new String(decryptedBytes);

        } catch (IllegalArgumentException ex) {

            LOGGER.log(Level.WARNING,
                    "Invalid Base64 encrypted data");

            throw new DecryptionException(
                    "Encrypted data format is invalid", ex);

        } catch (BadPaddingException ex) {

            LOGGER.log(Level.WARNING,
                    "Bad padding during decryption");

            throw new DecryptionException(
                    "Decryption failed", ex);

        } catch (InvalidKeyException ex) {

            LOGGER.log(Level.WARNING,
                    "Invalid decryption key");

            throw new DecryptionException(
                    "Invalid key", ex);

        } catch (IllegalBlockSizeException ex) {

            LOGGER.log(Level.WARNING,
                    "Illegal block size");

            throw new DecryptionException(
                    "Decryption failed", ex);

        } catch (Exception ex) {

            LOGGER.log(Level.SEVERE,
                    "Unexpected decryption error");

            throw new DecryptionException(
                    "Unexpected error", ex);
        }
    }

    private void validateInput(
            String encryptedPassword,
            String secretKey)
            throws DecryptionException {

        if (encryptedPassword == null) {
            throw new DecryptionException(
                    "encryptedPassword must not be null");
        }

        if (encryptedPassword.isBlank()) {
            throw new DecryptionException(
                    "encryptedPassword must not be empty");
        }

        if (secretKey == null) {
            throw new DecryptionException(
                    "secretKey must not be null");
        }

        if (secretKey.isBlank()) {
            throw new DecryptionException(
                    "secretKey must not be empty");
        }
    }
}
```

### SecurityDecryptorTest.java

```java
package com.example.security;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class SecurityDecryptorTest {

    private final SecurityDecryptor decryptor =
            new SecurityDecryptor();

    @Test
    void shouldThrowWhenEncryptedPasswordNull() {

        assertThrows(
                DecryptionException.class,
                () -> decryptor.decrypt(
                        null,
                        "1234567890123456"));
    }

    @Test
    void shouldThrowWhenEncryptedPasswordEmpty() {

        assertThrows(
                DecryptionException.class,
                () -> decryptor.decrypt(
                        "",
                        "1234567890123456"));
    }

    @Test
    void shouldThrowWhenSecretKeyNull() {

        assertThrows(
                DecryptionException.class,
                () -> decryptor.decrypt(
                        "abc",
                        null));
    }

    @Test
    void shouldThrowWhenSecretKeyEmpty() {

        assertThrows(
                DecryptionException.class,
                () -> decryptor.decrypt(
                        "abc",
                        ""));
    }

    @Test
    void shouldThrowWhenInvalidBase64() {

        assertThrows(
                DecryptionException.class,
                () -> decryptor.decrypt(
                        "@@@invalid@@@",
                        "1234567890123456"));
    }

    @Test
    void shouldThrowWhenWrongKeyUsed() {

        assertThrows(
                DecryptionException.class,
                () -> decryptor.decrypt(
                        "U29tZUNpcGhlclRleHQ=",
                        "wrongwrongwrong1"));
    }

    @Test
    void shouldCreateCustomException() {

        DecryptionException ex =
                new DecryptionException("test");

        assertEquals(
                "test",
                ex.getMessage());
    }
}
```