## 1. Mô tả ý đồ thiết kế quy trình 2 bước

Mục tiêu của quy trình là giảm nguy cơ AI sinh ra mã nguồn đúng nghiệp vụ nhưng kém hiệu năng.

* Bước 1: Yêu cầu AI sinh mã nguồn Java đúng chức năng phát hiện giao dịch đáng ngờ theo các quy tắc nghiệp vụ đã cho.
* Bước 2: Sử dụng một Prompt kiểm toán độc lập để đóng vai trò chuyên gia tối ưu thuật toán, phân tích độ phức tạp của lời giải ở Bước 1 và đề xuất phương án cải tiến.
* Cách làm này mô phỏng quy trình thực tế trong doanh nghiệp: một AI đóng vai trò Developer, AI thứ hai đóng vai trò Reviewer/Auditor.
* Nhờ đó có thể phát hiện các vấn đề như thuật toán O(N²) khi dữ liệu tăng lên 10.000 giao dịch và tối ưu xuống O(NlogN).

---

# BƯỚC 1 - CODE GENERATION PROMPT

Hãy đóng vai trò là một Senior Java Developer.

Nhiệm vụ của bạn là xây dựng chức năng phát hiện giao dịch đáng ngờ cho hệ thống FinTech SmartCheck.

## Mô hình dữ liệu

Lớp Transaction có các thuộc tính:

* String transactionId
* String cardId
* double amount
* String location
* LocalDateTime transactionTime

## Quy tắc nghiệp vụ

1. Nếu một giao dịch có giá trị lớn hơn 100000000 VND thì giao dịch đó phải được đánh dấu là đáng ngờ.

2. Nếu tồn tại hai giao dịch trên cùng một cardId được thực hiện tại hai địa điểm khác nhau trong khoảng thời gian dưới 10 phút thì cả hai giao dịch đều phải được đánh dấu là đáng ngờ.

Ví dụ:

* Giao dịch A:

    * cardId = CARD001
    * location = Hanoi
    * time = 2026-06-01T10:00

* Giao dịch B:

    * cardId = CARD001
    * location = Saigon
    * time = 2026-06-01T10:08

=> Cả A và B đều bị đánh dấu đáng ngờ.

## Yêu cầu kỹ thuật

* Tạo lớp FraudDetector.
* Tạo phương thức:

```java
public List<Transaction> detectFraud(List<Transaction> transactions)
```

* Sử dụng Java 8 Date Time API.
* Sử dụng LocalDateTime và Duration để tính khoảng cách thời gian.
* Không sử dụng thư viện bên thứ ba.
* Mã nguồn phải biên dịch được.

## Xử lý dữ liệu lỗi biên

* transactions == null → trả về danh sách rỗng.
* transactions rỗng → trả về danh sách rỗng.
* location null hoặc rỗng → bỏ qua quy tắc kiểm tra địa điểm nhưng vẫn kiểm tra số tiền.
* transactionTime null → bỏ qua kiểm tra thời gian.

## Định dạng đầu ra

1. Giải thích ngắn gọn thuật toán.
2. Phân tích độ phức tạp thời gian.
3. Sinh đầy đủ mã nguồn Java.

---

# BƯỚC 2 - PERFORMANCE AUDIT PROMPT

Hãy đóng vai trò là Java Performance Auditor và Algorithm Expert.

Tôi sẽ cung cấp mã nguồn FraudDetector được AI sinh ra ở bước trước.

Nhiệm vụ của bạn:

## Phân tích hiệu năng

1. Xác định độ phức tạp thời gian hiện tại.
2. Chỉ ra các đoạn mã gây ra độ phức tạp O(N²).
3. Giải thích tại sao khi số lượng giao dịch tăng lên 10000 phần tử thì hệ thống có nguy cơ chậm.

## Yêu cầu tối ưu

Hãy thiết kế lại thuật toán theo hướng:

* Gom nhóm giao dịch theo cardId.
* Sắp xếp từng nhóm theo transactionTime.
* Chỉ so sánh các giao dịch lân cận trong cùng cardId.
* Sử dụng Collections.sort hoặc Comparator.
* Giữ nguyên nghiệp vụ phát hiện gian lận.

## Mục tiêu

Đưa độ phức tạp từ:

```text
O(N²)
```

về:

```text
O(N log N)
```

hoặc tốt hơn nếu có thể.

## Ràng buộc

* Không thay đổi chữ ký phương thức detectFraud.
* Không thay đổi cấu trúc lớp Transaction.
* Vẫn sử dụng Java 8.
* Giải thích rõ sự khác biệt về độ phức tạp trước và sau tối ưu.

## Định dạng đầu ra

1. Phân tích điểm nghẽn hiệu năng.
2. Giải thích chiến lược tối ưu.
3. So sánh Big-O trước và sau.
4. Sinh lại toàn bộ mã nguồn Java đã tối ưu.

---

## 3. Minh chứng thực tế (Log Chat Bước 1)

### User

```text
Hãy đóng vai trò là một Senior Java Developer.

Xây dựng lớp FraudDetector với phương thức:

public List<Transaction> detectFraud(List<Transaction> transactions)

Áp dụng các quy tắc:

1. amount > 100000000 => fraud

2. Hai giao dịch cùng cardId,
khác location,
cách nhau dưới 10 phút
=> cả hai là fraud

Sử dụng LocalDateTime.

Phân tích độ phức tạp và sinh mã nguồn Java.
```

### AI

```java
public List<Transaction> detectFraud(List<Transaction> transactions) {

    List<Transaction> frauds = new ArrayList<>();

    if (transactions == null || transactions.isEmpty()) {
        return frauds;
    }

    for (Transaction t : transactions) {

        if (t.getAmount() > 100000000) {
            frauds.add(t);
        }

        for (Transaction other : transactions) {

            if (t == other) {
                continue;
            }

            if (t.getCardId().equals(other.getCardId())
                    && !t.getLocation().equals(other.getLocation())) {

                long minutes =
                        Math.abs(Duration.between(
                                t.getTransactionTime(),
                                other.getTransactionTime())
                                .toMinutes());

                if (minutes < 10) {
                    frauds.add(t);
                    frauds.add(other);
                }
            }
        }
    }

    return frauds;
}
```

### Phân tích AI bước 1

```text
Độ phức tạp thời gian: O(N²)

Do sử dụng hai vòng lặp lồng nhau để so sánh mọi cặp giao dịch.
```

---

## 4. Minh chứng thực tế (Log Chat Bước 2)

### User

```text
Hãy đóng vai trò là Java Performance Auditor.

Phân tích đoạn code FraudDetector trên.

Tìm điểm nghẽn hiệu năng khi số lượng giao dịch là 10000.

Đề xuất giải pháp gom nhóm theo cardId,
sắp xếp theo transactionTime,
và giảm độ phức tạp từ O(N²) xuống O(NlogN).
```

### AI

```text
Điểm nghẽn:

for(Transaction t : transactions)
    for(Transaction other : transactions)

Mỗi giao dịch được so sánh với tất cả giao dịch còn lại.

Độ phức tạp:

O(N²)

Với 10000 giao dịch:

10000 × 10000
= 100.000.000 lần so sánh

Điều này gây ảnh hưởng lớn đến hiệu năng.
```

### AI đề xuất tối ưu

```text
Bước 1:
Gom nhóm theo cardId bằng HashMap.

Bước 2:
Sắp xếp từng nhóm theo transactionTime.

Bước 3:
Chỉ so sánh các giao dịch lân cận trong cùng cardId.

Độ phức tạp:

O(N log N)
```

---

## 5. Mã nguồn Java tối ưu cuối cùng (O(N log N))

```java
import java.time.Duration;
import java.time.LocalDateTime;
import java.util.*;

public class FraudDetector {

    public List<Transaction> detectFraud(List<Transaction> transactions) {

        List<Transaction> frauds = new ArrayList<>();

        if (transactions == null || transactions.isEmpty()) {
            return frauds;
        }

        Set<Transaction> fraudSet = new HashSet<>();

        for (Transaction t : transactions) {
            if (t.getAmount() > 100000000) {
                fraudSet.add(t);
            }
        }

        Map<String, List<Transaction>> groups = new HashMap<>();

        for (Transaction t : transactions) {
            groups.computeIfAbsent(
                    t.getCardId(),
                    k -> new ArrayList<>())
                  .add(t);
        }

        for (List<Transaction> cardTransactions : groups.values()) {

            cardTransactions.sort(
                    Comparator.comparing(
                            Transaction::getTransactionTime));

            for (int i = 1; i < cardTransactions.size(); i++) {

                Transaction prev = cardTransactions.get(i - 1);
                Transaction curr = cardTransactions.get(i);

                if (prev.getLocation() == null
                        || curr.getLocation() == null) {
                    continue;
                }

                if (!prev.getLocation().equals(curr.getLocation())) {

                    long minutes =
                            Duration.between(
                                    prev.getTransactionTime(),
                                    curr.getTransactionTime())
                                    .abs()
                                    .toMinutes();

                    if (minutes < 10) {
                        fraudSet.add(prev);
                        fraudSet.add(curr);
                    }
                }
            }
        }

        return new ArrayList<>(fraudSet);
    }
}
```

### Kết quả kiểm toán

| Phiên bản | Thuật toán                | Độ phức tạp |
| --------- | ------------------------- | ----------- |
| Bước 1    | So sánh mọi cặp giao dịch | O(N²)       |
| Bước 2    | Group theo cardId + Sort  | O(N log N)  |