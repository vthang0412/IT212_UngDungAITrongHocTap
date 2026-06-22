# Prompt tối ưu

Hãy đóng vai trò là một Java Debugger (chuyên gia gỡ lỗi Java) có nhiều năm kinh nghiệm.

Nhiệm vụ của bạn là phân tích lỗi chương trình, xác định nguyên nhân gốc rễ (Root Cause), giải thích Stack Trace và đề xuất cách sửa chữa phù hợp theo nguyên tắc OOP và Clean Code.

## Mã nguồn gây lỗi

```java
import java.util.List;

public class UserManager {

    private List<String> users; // Danh sách chưa được khởi tạo

    public void addUser(String user) {
        users.add(user);
    }
}
```

## Stack Trace

```text
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "java.util.List.add(Object)" because "this.users" is null
    at UserManager.addUser(UserManager.java:7)
    at Main.main(Main.java:6)
```

## Yêu cầu và ràng buộc

1. Phân tích ý nghĩa của từng dòng trong Stack Trace.
2. Xác định chính xác nguyên nhân gốc rễ (Root Cause) gây ra NullPointerException.
3. Giải thích tại sao lỗi xảy ra tại dòng users.add(user).
4. Đề xuất cách sửa chữa an toàn nhất bằng cách khởi tạo danh sách bằng ArrayList.
5. Không được làm mất tính bao đóng (Encapsulation), trường users vẫn phải để private.
6. Không thay đổi chức năng hiện tại của lớp.
7. Sử dụng Java 11 trở lên.

## Định dạng đầu ra

* Phần 1: Phân tích Stack Trace.
* Phần 2: Root Cause.
* Phần 3: Giải pháp sửa lỗi.
* Phần 4: Mã nguồn Java hoàn chỉnh sau khi sửa.
* Phần 5: Giải thích ngắn gọn vì sao cách sửa này an toàn.

### Mã nguồn Java hoàn chỉnh và an toàn

```java
import java.util.ArrayList;
import java.util.List;

public class UserManager {

    private final List<String> users;

    public UserManager() {
        this.users = new ArrayList<>();
    }

    public void addUser(String user) {
        users.add(user);
    }

    public List<String> getUsers() {
        return new ArrayList<>(users);
    }
}
```

### Vì sao cách sửa này an toàn?

* `users` được khởi tạo trong constructor nên không còn giá trị `null`.
* Sử dụng `ArrayList` là cách triển khai phổ biến cho `List`.
* Thuộc tính `users` vẫn là `private`, đảm bảo tính bao đóng.
* Khai báo `final` giúp tránh việc gán lại tham chiếu danh sách sau khi khởi tạo.
* `getUsers()` trả về một bản sao (`new ArrayList<>(users)`) thay vì trả về danh sách nội bộ, tránh việc mã bên ngoài sửa trực tiếp trạng thái của đối tượng.