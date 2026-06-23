# Đáp án: **Phương án B**

## 1. Vì sao phương án B là lựa chọn tối ưu nhất?

Bài toán tính thuế TNCN lũy tiến là một bài toán có:

* Nhiều bước trung gian.
* Nhiều công thức phụ thuộc lẫn nhau.
* Nhiều ranh giới dễ sai (5 triệu, 10 triệu, 18 triệu,...).
* Dễ phát sinh lỗi làm tròn số tiền.
* Cần đảm bảo tính đúng nghiệp vụ hơn là tối ưu hiệu năng.

Phương án B áp dụng đúng tư tưởng **Chain-of-Thought (CoT)**: yêu cầu AI phân tích từng bước trước khi sinh code.

### Phân tích theo cấu trúc 5 thành phần của Prompt

#### 1. Role (Vai trò)

> "Hãy đóng vai trò là một Java Developer chuyên nghiệp kiêm chuyên gia tài chính"

Điều này giúp AI kết hợp:

* Kiến thức Java
* Kiến thức nghiệp vụ tài chính/thuế

=> Giảm nguy cơ viết đúng code nhưng sai nghiệp vụ.

---

#### 2. Task (Nhiệm vụ)

> Viết class Java tính thuế TNCN lũy tiến

Nhiệm vụ được mô tả rất rõ ràng.

AI biết chính xác cần:

* Tính thu nhập chịu thuế
* Tính thuế lũy tiến từng phần
* Sinh class Java hoàn chỉnh

---

#### 3. Context (Ngữ cảnh)

Prompt cung cấp:

* Giảm trừ bản thân: 11 triệu
* Người phụ thuộc: 4.4 triệu/người
* Bảo hiểm: 10.5%
* Các bậc thuế cụ thể

AI không phải suy đoán.

=> Giảm Hallucination.

---

#### 4. Constraints (Ràng buộc)

Prompt quy định:

* Java 17
* BigDecimal

Đây là điểm cực kỳ quan trọng.

Nếu dùng:

```java
double
```

sẽ xuất hiện lỗi:

```java
0.1 + 0.2 != 0.3
```

Trong tài chính:

```java
BigDecimal
```

là lựa chọn chuẩn.

---

#### 5. Output Format (Định dạng đầu ra)

Prompt yêu cầu:

1. Phân tích thu nhập tính thuế.
2. Phân tích từng bậc thuế.
3. Dry-run ví dụ thực tế.
4. Sau đó mới viết code.

Điều này buộc AI:

* Kiểm tra logic trước.
* Giảm lỗi biên.
* Dễ phát hiện sai sót.

Đây chính là tinh thần của **Chain-of-Thought Prompting**.

---

# 2. Tại sao phương án B phù hợp với Chain-of-Thought?

CoT yêu cầu:

> Chia bài toán lớn thành nhiều bước suy luận nhỏ.

Prompt B yêu cầu AI:

### Bước 1

Xác định:

```text
Thu nhập tính thuế
=
Tổng thu nhập
- Giảm trừ bản thân
- Giảm trừ người phụ thuộc
- Bảo hiểm
```

---

### Bước 2

Phân tích từng bậc thuế:

```text
0 → 5 triệu
5%

5 → 10 triệu
10%

...
```

---

### Bước 3

Dry-run bằng tay.

Ví dụ:

```text
30 triệu
1 người phụ thuộc
không bảo hiểm
```

AI phải tự kiểm chứng kết quả.

---

### Bước 4

Mới sinh code.

Đây là quy trình:

```text
Phân tích
→ Kiểm chứng
→ Sinh mã
```

thay vì:

```text
Đọc đề
→ Viết code ngay
```

nên độ chính xác cao hơn đáng kể.

---

# 3. Loại trừ phương án A

## Prompt A

> "Hãy viết hàm Java tính thuế thu nhập cá nhân lũy tiến theo biểu thuế Việt Nam hiện hành. Hãy tối ưu nhất."

### Nhược điểm 1: Thiếu Context

Không cung cấp:

* Giảm trừ bản thân
* Giảm trừ người phụ thuộc
* Bảo hiểm

AI phải tự đoán.

---

### Nhược điểm 2: Không có CoT

AI sẽ:

```text
Đọc đề
→ Viết code ngay
```

Không bắt buộc suy luận từng bước.

---

### Nhược điểm 3: "Tối ưu nhất" mơ hồ

AI không biết:

* Tối ưu hiệu năng?
* Tối ưu bộ nhớ?
* Tối ưu độ chính xác?

=> Dễ sinh mã không phù hợp.

---

### Nhược điểm 4: Không yêu cầu BigDecimal

AI có thể dùng:

```java
double
```

gây sai số tiền tệ.

---

# 4. Loại trừ phương án C

## Prompt C

> "Hãy đóng vai Senior Developer. Hãy viết một class Java tính thuế TNCN lũy tiến sử dụng Java Stream API song song để tính toán cực nhanh các bậc thuế lũy tiến nhằm tối ưu hiệu năng."

### Nhược điểm 1: Tối ưu sai mục tiêu

Bài toán thuế chỉ có:

```text
7 bậc thuế
```

Không cần:

```java
parallelStream()
```

---

### Nhược điểm 2: Hiệu năng không phải vấn đề

Ưu tiên là:

```text
Đúng nghiệp vụ
```

không phải:

```text
Nhanh hơn vài micro giây
```

---

### Nhược điểm 3: Tăng độ phức tạp

Sử dụng:

```java
Stream API
parallelStream()
```

khiến:

* Khó đọc
* Khó bảo trì
* Khó kiểm thử

---

### Nhược điểm 4: Không có CoT

Prompt không yêu cầu:

* Phân tích
* Dry-run
* Kiểm chứng

=> Dễ sinh lỗi logic.

---

# 5. Mã nguồn Java hoàn chỉnh theo Prompt B

```java
import java.math.BigDecimal;
import java.math.RoundingMode;

public class PersonalIncomeTaxCalculator {

    private static final BigDecimal PERSONAL_DEDUCTION =
            new BigDecimal("11000000");

    private static final BigDecimal DEPENDENT_DEDUCTION =
            new BigDecimal("4400000");

    private static final BigDecimal INSURANCE_RATE =
            new BigDecimal("0.105");

    public static BigDecimal calculateTax(
            BigDecimal totalIncome,
            int dependents,
            boolean hasInsurance) {

        BigDecimal insurance = BigDecimal.ZERO;

        if (hasInsurance) {
            insurance = totalIncome.multiply(INSURANCE_RATE);
        }

        BigDecimal taxableIncome = totalIncome
                .subtract(PERSONAL_DEDUCTION)
                .subtract(DEPENDENT_DEDUCTION.multiply(
                        BigDecimal.valueOf(dependents)))
                .subtract(insurance);

        if (taxableIncome.compareTo(BigDecimal.ZERO) <= 0) {
            return BigDecimal.ZERO;
        }

        return calculateProgressiveTax(taxableIncome)
                .setScale(0, RoundingMode.HALF_UP);
    }

    private static BigDecimal calculateProgressiveTax(
            BigDecimal income) {

        BigDecimal tax = BigDecimal.ZERO;

        BigDecimal[] limits = {
                new BigDecimal("5000000"),
                new BigDecimal("10000000"),
                new BigDecimal("18000000"),
                new BigDecimal("32000000"),
                new BigDecimal("52000000"),
                new BigDecimal("80000000")
        };

        BigDecimal[] rates = {
                new BigDecimal("0.05"),
                new BigDecimal("0.10"),
                new BigDecimal("0.15"),
                new BigDecimal("0.20"),
                new BigDecimal("0.25"),
                new BigDecimal("0.30"),
                new BigDecimal("0.35")
        };

        BigDecimal previousLimit = BigDecimal.ZERO;

        for (int i = 0; i < limits.length; i++) {

            if (income.compareTo(limits[i]) > 0) {

                BigDecimal taxablePart =
                        limits[i].subtract(previousLimit);

                tax = tax.add(
                        taxablePart.multiply(rates[i]));

                previousLimit = limits[i];
            } else {

                BigDecimal taxablePart =
                        income.subtract(previousLimit);

                tax = tax.add(
                        taxablePart.multiply(rates[i]));

                return tax;
            }
        }

        BigDecimal remaining =
                income.subtract(limits[limits.length - 1]);

        tax = tax.add(
                remaining.multiply(
                        rates[rates.length - 1]));

        return tax;
    }

    public static void main(String[] args) {

        BigDecimal income =
                new BigDecimal("30000000");

        BigDecimal tax =
                calculateTax(income, 1, false);

        System.out.println("Thuế phải nộp: "
                + tax.toPlainString() + " VND");
    }
}
```