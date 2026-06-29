## 1. Nội dung Prompt gỡ lỗi

Bạn là một Senior Java Debugger và Database Troubleshooting Expert.

Nhiệm vụ của bạn là phân tích mã nguồn Java và Stack Trace bên dưới để tìm nguyên nhân gây lỗi.

Yêu cầu thực hiện theo các bước:

1. Đọc mã nguồn.
2. Đọc Stack Trace.
3. Giải thích ý nghĩa của từng phần quan trọng trong Stack Trace.
4. Xác định Root Cause (nguyên nhân gốc rễ).
5. Chỉ rõ dòng code gây lỗi.
6. Đề xuất giải pháp khắc phục.
7. Refactor mã nguồn theo hướng an toàn hơn.

====================
MÃ NGUỒN GÂY LỖI
================

```java
import java.sql.Connection;
import java.sql.PreparedStatement;

public class TransactionRepository {

    private Connection connection;

    public void saveTransaction(
            String transactionId,
            Long userId,
            double amount) throws Exception {

        // Lỗi logic:
        // Không kiểm tra userId có tồn tại hay không

        String sql =
                "INSERT INTO transactions (id, user_id, amount) VALUES (?, ?, ?)";

        PreparedStatement ps =
                connection.prepareStatement(sql);

        ps.setString(1, transactionId);
        ps.setLong(2, userId);
        ps.setDouble(3, amount);

        ps.executeUpdate();
    }
}
```

====================
STACK TRACE
===========

```text
org.h2.jdbc.JdbcSQLIntegrityConstraintViolationException:
Referential integrity constraint violation:
"CONSTRAINT_FOREIGN_KEY_USER:
PUBLIC.TRANSACTIONS FOREIGN KEY(USER_ID)
REFERENCES PUBLIC.USERS(ID) (99)";

SQL statement:

INSERT INTO transactions
(id, user_id, amount)
VALUES (?, ?, ?)

[23506-214]

at org.h2.message.DbException.getJdbcSQLException(DbException.java:502)

at TransactionRepository.saveTransaction(TransactionRepository.java:13)

at Main.main(Main.java:10)
```

====================
YÊU CẦU BẮT BUỘC
================

Hãy đóng vai chuyên gia gỡ lỗi Java.

Phải giải thích:

* Foreign Key Constraint là gì.
* Ý nghĩa mã lỗi 23506.
* Vì sao userId = 99 gây lỗi.
* Vì sao lỗi xuất hiện ở TransactionRepository.java dòng 13.

====================
YÊU CẦU REFACTOR
================

Tối ưu lại code:

1. Tạo UserNotFoundException.

2. Trước khi INSERT:

* Kiểm tra userId có tồn tại trong bảng users hay không.

Ví dụ:

select count(*)
from users
where id = ?

3. Nếu user không tồn tại:

throw new UserNotFoundException(
"User not found: " + userId
);

4. Chỉ INSERT khi user tồn tại.

5. Sử dụng try-with-resources.

6. Giữ nguyên JDBC thuần.

====================
OUTPUT MONG MUỐN
================

Xuất theo thứ tự:

1. Phân tích Stack Trace.
2. Root Cause.
3. Giải pháp.
4. Mã nguồn UserNotFoundException.java.
5. Mã nguồn TransactionRepository.java đã sửa.
6. Giải thích vì sao phiên bản mới an toàn hơn.

---

# 2. Minh chứng chạy thực tế (Log phản hồi AI)

## User

```text
[Prompt ở mục 1]
```

## AI Response

### Phân tích Stack Trace

```text
Lỗi phát sinh:

JdbcSQLIntegrityConstraintViolationException

Đây là ngoại lệ do H2 Database ném ra khi dữ liệu vi phạm ràng buộc toàn vẹn dữ liệu (Integrity Constraint).

Thông báo:

Referential integrity constraint violation

cho biết đã vi phạm khóa ngoại (Foreign Key Constraint).

Constraint:

CONSTRAINT_FOREIGN_KEY_USER

nghĩa là cột:

transactions.user_id

đang tham chiếu tới:

users.id
```

---

### Ý nghĩa mã lỗi 23506

```text
23506 là mã lỗi chuẩn của H2 Database.

Ý nghĩa:

Foreign Key Constraint Violation

Một bản ghi đang cố tham chiếu tới bản ghi cha không tồn tại.
```

---

### Tại sao userId = 99 gây lỗi?

```text
Bảng transactions có:

FOREIGN KEY(user_id)
REFERENCES users(id)

Khi thực hiện:

INSERT INTO transactions
(id, user_id, amount)
VALUES (?, ?, ?)

với:

userId = 99

Database sẽ kiểm tra:

select *
from users
where id = 99

Nếu không tìm thấy user id = 99
thì transaction không được phép tạo.

Do đó H2 ném:

JdbcSQLIntegrityConstraintViolationException
```

---

### Vì sao lỗi xuất hiện tại dòng 13?

```text
Stack Trace:

at TransactionRepository.saveTransaction(TransactionRepository.java:13)

Dòng 13 tương ứng:

ps.executeUpdate();

Đây là thời điểm JDBC gửi câu lệnh INSERT xuống Database.

Database kiểm tra ràng buộc khóa ngoại và phát hiện user_id không tồn tại.
```

---

### Root Cause

```text
Nguyên nhân gốc rễ không nằm ở JDBC.

Nguyên nhân là lỗi nghiệp vụ:

Chương trình không xác minh sự tồn tại của userId trước khi tạo giao dịch.

Hệ thống hoàn toàn phụ thuộc vào Database để phát hiện lỗi.
```

---

### UserNotFoundException.java

```java
public class UserNotFoundException extends Exception {

    public UserNotFoundException(String message) {
        super(message);
    }
}
```

---

### TransactionRepository.java (Đã sửa)

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class TransactionRepository {

    private Connection connection;

    public void saveTransaction(
            String transactionId,
            Long userId,
            double amount)
            throws Exception {

        if (!userExists(userId)) {

            throw new UserNotFoundException(
                    "User not found: " + userId);
        }

        String insertSql =
                "INSERT INTO transactions " +
                "(id, user_id, amount) " +
                "VALUES (?, ?, ?)";

        try (PreparedStatement ps =
                     connection.prepareStatement(insertSql)) {

            ps.setString(1, transactionId);
            ps.setLong(2, userId);
            ps.setDouble(3, amount);

            ps.executeUpdate();
        }
    }

    private boolean userExists(Long userId)
            throws Exception {

        String sql =
                "SELECT COUNT(*) " +
                "FROM users " +
                "WHERE id = ?";

        try (PreparedStatement ps =
                     connection.prepareStatement(sql)) {

            ps.setLong(1, userId);

            try (ResultSet rs =
                         ps.executeQuery()) {

                if (rs.next()) {

                    return rs.getInt(1) > 0;
                }
            }
        }

        return false;
    }
}
```

---

### Vì sao phiên bản mới an toàn hơn?

```text
1. Kiểm tra dữ liệu trước khi INSERT.

2. Phát hiện lỗi nghiệp vụ sớm.

3. Không phụ thuộc hoàn toàn vào Database.

4. Ném UserNotFoundException có ý nghĩa rõ ràng.

5. Dễ xử lý ở tầng Service hoặc Controller.

6. Sử dụng try-with-resources giúp tự động đóng PreparedStatement.

7. Tránh phát sinh JdbcSQLIntegrityConstraintViolationException không mong muốn.

8. Tăng tính ổn định và khả năng bảo trì của hệ thống.
```

### Kết luận

```text
Root Cause:
userId không tồn tại trong bảng users nhưng vẫn được dùng để tạo giao dịch.

Giải pháp:
Xác thực sự tồn tại của user trước khi INSERT và ném UserNotFoundException nếu không tìm thấy.

Kết quả:
Ngăn lỗi Foreign Key Constraint ngay từ tầng ứng dụng thay vì để Database phát hiện sau cùng.
```