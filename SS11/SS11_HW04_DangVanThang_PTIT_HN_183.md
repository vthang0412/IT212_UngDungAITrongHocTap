# 1. Mega-Prompt

```markdown
# Vai trò (Role)

Bạn là Senior Java QA Automation Engineer với hơn 10 năm kinh nghiệm về Java, JUnit 5, Mockito, TDD và kiểm thử bảo mật (Security Testing).

# Mục tiêu (Objective)

Chỉ với MỘT lần trả lời, hãy thực hiện toàn bộ các công việc sau:

1. Sinh bộ Mock Data dạng JSON.
2. Giả định logic của phương thức:
   login(String username, String password)
3. Sinh mã nguồn Unit Test bằng JUnit 5 để kiểm thử phương thức trên.
4. Giải thích ngắn gọn từng Test Case.

# Ngữ cảnh (Context)

Dự án hiện chưa có Database test nên toàn bộ dữ liệu sẽ được Mock trực tiếp từ file JSON.

Không có source code của hàm login(), hãy tự giả định logic hợp lý dựa trên tên hàm và các quy tắc sau:

- username tồn tại và password đúng → LOGIN_SUCCESS
- password sai → INVALID_PASSWORD
- tài khoản bị khóa → ACCOUNT_LOCKED
- phát hiện chuỗi SQL Injection → INVALID_INPUT
- password rỗng → EMPTY_PASSWORD

Mock Data phải gồm đúng 5 người dùng đại diện cho các trường hợp:

1. Đăng nhập thành công.
2. Sai mật khẩu.
3. Tài khoản bị khóa.
4. SQL Injection.
5. Mật khẩu rỗng.

# Ràng buộc (Constraints)

- Chỉ tạo đúng 5 bản ghi.
- Dữ liệu ở định dạng JSON hợp lệ.
- Không sử dụng Database.
- Unit Test sử dụng:
    - Java
    - JUnit 5
- Giả lập việc đọc dữ liệu JSON trong mã nguồn.
- Mỗi Test Case phải có assert rõ ràng.
- Đặt tên Test theo quy tắc should...When...

# Định dạng đầu ra (Output Format)

Trả lời theo đúng thứ tự:

1. Giả định logic của login().
2. Bộ Mock Data (JSON).
3. Mã nguồn JUnit 5.
4. Giải thích ý nghĩa từng Test Case.
```

---

# 2. Bộ Mock Data (JSON)

```json
[
  {
    "username": "admin",
    "password": "Admin@123",
    "accountLocked": false,
    "expectedResult": "LOGIN_SUCCESS"
  },
  {
    "username": "admin",
    "password": "WrongPassword",
    "accountLocked": false,
    "expectedResult": "INVALID_PASSWORD"
  },
  {
    "username": "lockedUser",
    "password": "Locked@123",
    "accountLocked": true,
    "expectedResult": "ACCOUNT_LOCKED"
  },
  {
    "username": "' OR '1'='1",
    "password": "anything",
    "accountLocked": false,
    "expectedResult": "INVALID_INPUT"
  },
  {
    "username": "user01",
    "password": "",
    "accountLocked": false,
    "expectedResult": "EMPTY_PASSWORD"
  }
]
```

---

# 3. Mã nguồn JUnit 5

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

class UserAuthenticationTest {

    private final UserAuthentication authentication = new UserAuthentication();

    @Test
    void shouldReturnLoginSuccessWhenUsernameAndPasswordAreCorrect() {
        String result = authentication.login("admin", "Admin@123");
        assertEquals("LOGIN_SUCCESS", result);
    }

    @Test
    void shouldReturnInvalidPasswordWhenPasswordIsIncorrect() {
        String result = authentication.login("admin", "WrongPassword");
        assertEquals("INVALID_PASSWORD", result);
    }

    @Test
    void shouldReturnAccountLockedWhenAccountIsLocked() {
        String result = authentication.login("lockedUser", "Locked@123");
        assertEquals("ACCOUNT_LOCKED", result);
    }

    @Test
    void shouldReturnInvalidInputWhenSqlInjectionDetected() {
        String result = authentication.login("' OR '1'='1", "anything");
        assertEquals("INVALID_INPUT", result);
    }

    @Test
    void shouldReturnEmptyPasswordWhenPasswordIsBlank() {
        String result = authentication.login("user01", "");
        assertEquals("EMPTY_PASSWORD", result);
    }
}
```

---

# 4. Giải thích các Test Case

| Test Case                                                   | Mục đích                                                                                       |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `shouldReturnLoginSuccessWhenUsernameAndPasswordAreCorrect` | Kiểm tra đăng nhập thành công khi tên đăng nhập và mật khẩu hợp lệ.                            |
| `shouldReturnInvalidPasswordWhenPasswordIsIncorrect`        | Kiểm tra hệ thống từ chối khi mật khẩu không đúng.                                             |
| `shouldReturnAccountLockedWhenAccountIsLocked`              | Kiểm tra tài khoản bị khóa không thể đăng nhập dù nhập đúng mật khẩu.                          |
| `shouldReturnInvalidInputWhenSqlInjectionDetected`          | Kiểm tra hệ thống phát hiện và từ chối chuỗi SQL Injection nhằm đảm bảo an toàn bảo mật.       |
| `shouldReturnEmptyPasswordWhenPasswordIsBlank`              | Kiểm tra xử lý trường hợp người dùng để trống mật khẩu, tránh lỗi và đảm bảo xác thực đầu vào. |