## 1. Phân tích vì sao prompt thô thất bại

Prompt:

> "Code này có chạy được không?"

là một prompt quá ngắn và thiếu ngữ cảnh nên AI thường chỉ thực hiện kiểm tra ở mức bề mặt.

### Thiếu Role (Vai trò)

Prompt không yêu cầu AI đóng vai:

* QA Engineer
* Tester
* Code Reviewer

Do đó AI có xu hướng kiểm tra:

* lỗi cú pháp
* lỗi biên dịch

thay vì phân tích các lỗi logic và trường hợp biên.

---

### Thiếu Goal (Mục tiêu)

Prompt chỉ hỏi:

> "có chạy được không?"

AI sẽ hiểu là:

* Có compile được không?
* Có lỗi syntax không?

Trong đoạn code này:

```java
for (int num : numbers)
```

và

```java
return (double) sum / numbers.size();
```

đều hợp lệ về mặt cú pháp.

Vì vậy AI dễ trả lời:

> "Code chạy được."

---

### Thiếu Context (Ngữ cảnh)

Không nói rằng:

* đây là hàm production
* cần kiểm tra độ ổn định
* cần phân tích edge cases

nên AI không tập trung vào các tình huống đặc biệt.

---

### Thiếu Constraint (Ràng buộc)

Prompt không yêu cầu:

* tìm NullPointerException
* tìm lỗi chia cho 0
* liệt kê trường hợp biên

nên AI có thể bỏ sót.

---

### Thiếu Format (Định dạng đầu ra)

Không yêu cầu:

* phân tích lỗi
* đề xuất sửa
* sinh test case

nên AI chỉ trả lời vài dòng ngắn gọn.

---

## 2. Prompt tối ưu mới

Hãy đóng vai một Chuyên gia Kiểm thử phần mềm (Senior QA Engineer và Code Reviewer) có kinh nghiệm phân tích lỗi Java Production.

Mục tiêu của bạn là rà soát đoạn mã Java dưới đây để tìm tất cả lỗi logic, lỗi runtime và các trường hợp biên (Edge Cases) có thể làm ứng dụng bị crash hoặc trả về kết quả không chính xác.

Ngữ cảnh:

* Dự án sử dụng Java 17.
* Hàm calculateAverage() được sử dụng trong môi trường production.
* Tính ổn định và khả năng xử lý dữ liệu đầu vào bất thường là yêu cầu bắt buộc.

Ràng buộc:

1. Phân tích từng dòng code và chỉ ra các lỗi tiềm ẩn.
2. Liệt kê tất cả các edge cases có thể xảy ra.
3. Chỉ rõ loại Exception tương ứng.
4. Sinh lại mã nguồn Java đã sửa lỗi hoàn chỉnh.
5. Bổ sung chú thích tiếng Việt giải thích từng bước.
6. Sinh đầy đủ JUnit 5 test case kiểm thử:

   * numbers == null
   * numbers rỗng
   * danh sách có 1 phần tử
   * danh sách nhiều phần tử
   * danh sách chứa số âm
7. Tuân thủ Clean Code và OOP.

Định dạng đầu ra:

* Phân tích lỗi
* Danh sách edge cases
* Mã nguồn đã sửa
* Mã nguồn JUnit Test

Đoạn code cần kiểm tra:

public class AverageCalculator {

```
public static double calculateAverage(List<Integer> numbers) {

    int sum = 0;

    for (int num : numbers) {
        sum += num;
    }

    return (double) sum / numbers.size();
}
```

}

---

## 3. Mã nguồn Java đã sửa lỗi

```java
import java.util.List;

public class AverageCalculator {

    /**
     * Tính giá trị trung bình của danh sách số nguyên
     *
     * @param numbers danh sách số nguyên
     * @return giá trị trung bình
     * @throws IllegalArgumentException nếu danh sách null hoặc rỗng
     */
    public static double calculateAverage(List<Integer> numbers) {

        // Kiểm tra null
        if (numbers == null) {
            throw new IllegalArgumentException(
                "Danh sách numbers không được null"
            );
        }

        // Kiểm tra danh sách rỗng
        if (numbers.isEmpty()) {
            throw new IllegalArgumentException(
                "Danh sách numbers không được rỗng"
            );
        }

        int sum = 0;

        for (Integer num : numbers) {

            // Tránh trường hợp phần tử null
            if (num == null) {
                throw new IllegalArgumentException(
                    "Danh sách không được chứa phần tử null"
                );
            }

            sum += num;
        }

        return (double) sum / numbers.size();
    }
}
```

---

## 4. JUnit 5 Test Case

```java
import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.Collections;

import static org.junit.jupiter.api.Assertions.*;

class AverageCalculatorTest {

    @Test
    void shouldThrowExceptionWhenListIsNull() {

        IllegalArgumentException exception =
                assertThrows(
                        IllegalArgumentException.class,
                        () -> AverageCalculator.calculateAverage(null)
                );

        assertEquals(
                "Danh sách numbers không được null",
                exception.getMessage()
        );
    }

    @Test
    void shouldThrowExceptionWhenListIsEmpty() {

        IllegalArgumentException exception =
                assertThrows(
                        IllegalArgumentException.class,
                        () -> AverageCalculator.calculateAverage(
                                Collections.emptyList()
                        )
                );

        assertEquals(
                "Danh sách numbers không được rỗng",
                exception.getMessage()
        );
    }

    @Test
    void shouldCalculateAverageForSingleElement() {

        double result =
                AverageCalculator.calculateAverage(
                        Collections.singletonList(10)
                );

        assertEquals(10.0, result);
    }

    @Test
    void shouldCalculateAverageForMultipleElements() {

        double result =
                AverageCalculator.calculateAverage(
                        Arrays.asList(10, 20, 30, 40)
                );

        assertEquals(25.0, result);
    }

    @Test
    void shouldCalculateAverageForNegativeNumbers() {

        double result =
                AverageCalculator.calculateAverage(
                        Arrays.asList(-10, -20, -30)
                );

        assertEquals(-20.0, result);
    }

    @Test
    void shouldThrowExceptionWhenListContainsNullElement() {

        IllegalArgumentException exception =
                assertThrows(
                        IllegalArgumentException.class,
                        () -> AverageCalculator.calculateAverage(
                                Arrays.asList(10, null, 30)
                        )
                );

        assertEquals(
                "Danh sách không được chứa phần tử null",
                exception.getMessage()
        );
    }
}
```

### Các lỗi mà QA cần phát hiện

| Trường hợp biên               | Lỗi phát sinh                             |
| ----------------------------- | ----------------------------------------- |
| `numbers == null`             | `NullPointerException` tại vòng for       |
| `numbers.isEmpty()`           | Chia cho 0 → kết quả không hợp lệ (`NaN`) |
| Danh sách chứa phần tử `null` | `NullPointerException` khi unboxing       |
| Danh sách rất lớn             | Có thể tràn số nguyên (`int overflow`)    |
| Danh sách hợp lệ              | Trả về giá trị trung bình bình thường     |

=> Prompt mới tốt hơn vì đã cung cấp đầy đủ **Role + Goal + Context + Constraint + Format**, buộc AI phải thực hiện kiểm thử theo tư duy QA thay vì chỉ kiểm tra cú pháp.