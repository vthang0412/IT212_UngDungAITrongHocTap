## 1. Phân tích lý do Prompt thô dễ bỏ sót lỗi

Prompt:

> "Mã này bị lỗi gì?"

là một prompt quá ngắn và thiếu ngữ cảnh nên rất dễ khiến AI bỏ sót lỗi logic.

### Các vấn đề của prompt thô

### Thiếu ngữ cảnh thực thi

AI chỉ nhìn vào mã nguồn mà không biết:

* Kết quả mong đợi là gì.
* Kết quả thực tế là gì.
* Trường hợp kiểm thử nào đang thất bại.

Do đó AI có thể kết luận:

* Code biên dịch thành công.
* Không có lỗi cú pháp.
* Không có exception.

Và bỏ qua lỗi nghiệp vụ.

---

### Không có ca kiểm thử (Test Case)

Ví dụ với dữ liệu:

```java
int[] arr = {1, 2, 3, 4};
```

Mảng không chứa phần tử trùng lặp.

Nhưng chương trình lại trả về:

```java
1
```

Đây mới là bằng chứng cho thấy logic sai.

Nếu không có test case, AI rất dễ không phát hiện lỗi.

---

### Không yêu cầu phân tích logic biên

Lỗi nằm ở đây:

```java
for (int j = i; j < arr.length; j++)
```

Ngay lần đầu:

```java
i = 0
j = 0
```

nên:

```java
arr[0] == arr[0]
```

luôn đúng.

Kết quả:

```java
return arr[0];
```

được thực hiện ngay lập tức.

Đây là lỗi logic (Logical Bug), không phải lỗi cú pháp.

---

### Không có yêu cầu tối ưu

Prompt chỉ hỏi:

> "Mã này bị lỗi gì?"

AI có thể chỉ sửa:

```java
for (int j = i + 1; ...)
```

nhưng vẫn giữ độ phức tạp:

```text
O(N²)
```

và bỏ lỡ cơ hội cải tiến bằng HashSet.

---

## 2. Prompt tối ưu mới

Bạn hãy đóng vai trò là một Code Auditor và Java Performance Expert.

Nhiệm vụ của bạn là phân tích đoạn mã Java dưới đây, xác định lỗi logic, giải thích nguyên nhân gốc rễ và đề xuất giải pháp tối ưu hơn.

## Mã nguồn

```java
public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {

        for (int i = 0; i < arr.length; i++) {

            for (int j = i; j < arr.length; j++) {

                if (arr[i] == arr[j]) {

                    return arr[i];

                }

            }

        }

        return null;

    }

}
```

## Ca kiểm thử thực tế

```java
int[] arr = {1, 2, 3, 4};
```

Kỳ vọng:

```text
null
```

Vì không có phần tử trùng lặp.

Kết quả thực tế:

```text
1
```

## Yêu cầu

1. Phân tích chi tiết lỗi logic của chương trình.
2. Giải thích vì sao chương trình luôn trả về phần tử đầu tiên của mảng.
3. Chỉ ra dòng mã gây lỗi.
4. Phân tích độ phức tạp hiện tại O(N²).
5. Viết lại giải pháp sử dụng HashSet để giảm độ phức tạp xuống O(N).
6. Giữ nguyên chữ ký phương thức:
   public static Integer findDuplicate(int[] arr)
7. Trả về mã Java hoàn chỉnh trong khối code markdown.
8. Giải thích ngắn gọn lợi ích của HashSet đối với bài toán này.

## 3. Mã nguồn Java đã sửa bằng HashSet

```java
import java.util.HashSet;
import java.util.Set;

public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {

        if (arr == null || arr.length == 0) {
            return null;
        }

        Set<Integer> seen = new HashSet<>();

        for (int number : arr) {
            if (!seen.add(number)) {
                return number;
            }
        }

        return null;
    }

    public static void main(String[] args) {

        int[] arr1 = {1, 2, 3, 4};
        System.out.println(findDuplicate(arr1));

        int[] arr2 = {5, 8, 3, 8, 9};
        System.out.println(findDuplicate(arr2));
    }
}
```

### Ưu điểm của phiên bản mới

| Tiêu chí               | Phiên bản cũ       | Phiên bản mới |
| ---------------------- | ------------------ | ------------- |
| Cấu trúc dữ liệu       | Vòng lặp lồng nhau | HashSet       |
| Độ phức tạp thời gian  | O(N²)              | O(N)          |
| Dễ phát sinh lỗi logic | Cao                | Thấp          |
| Khả năng mở rộng       | Kém                | Tốt           |
| Khả năng đọc hiểu      | Trung bình         | Tốt           |

HashSet cho phép kiểm tra một phần tử đã xuất hiện hay chưa với chi phí trung bình O(1), nhờ đó toàn bộ thuật toán chỉ cần duyệt mảng một lần và đạt độ phức tạp O(N).