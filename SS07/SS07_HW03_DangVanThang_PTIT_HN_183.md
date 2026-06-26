# 1. Phân tích các lỗi vi phạm Clean Code của mã nguồn ban đầu

## 1. Đặt tên biến không rõ nghĩa

```java
String l;
double t;
BufferedReader r;
PrintWriter w;
String[] p;
```

Các tên biến:

* `l` → line
* `t` → totalAmount
* `r` → reader
* `w` → writer
* `p` → fields

không mô tả đúng ý nghĩa nghiệp vụ.

---

## 2. Vi phạm SRP (Single Responsibility Principle)

Phương thức:

```java
gen(...)
```

đang thực hiện cùng lúc:

* Đọc file
* Parse dữ liệu CSV
* Lọc đơn hàng
* Tính tổng tiền
* Ghi báo cáo
* Logging

Một phương thức có quá nhiều trách nhiệm.

---

## 3. Xử lý ngoại lệ kém

```java
throws Exception
```

là anti-pattern.

Không phân biệt:

```java
IOException
NumberFormatException
```

Không có cơ chế bỏ qua dữ liệu lỗi.

---

## 4. Quản lý tài nguyên thủ công

```java
r.close();
w.close();
```

Nếu xảy ra Exception trước đó:

```java
close()
```

sẽ không được gọi.

Nên dùng:

```java
try-with-resources
```

---

## 5. Magic Number

```java
if (amt > 100)
```

Số:

```java
100
```

là hardcode.

Nên đưa thành:

```java
minimumAmount
```

---

## 6. Logging sai chuẩn

```java
System.out.println(...)
```

Không phù hợp ứng dụng doanh nghiệp.

Nên dùng:

```java
@Slf4j
log.info(...)
```

---

## 7. Thiếu kiểm tra dữ liệu đầu vào

Không kiểm tra:

```java
f == null
out == null
```

hoặc

```java
f.isBlank()
```

---

# 2. Chuỗi Prompt Refinement Chain

## Vòng 1 — Robustness

Bạn là Senior Java Engineer.

Hãy refactor đoạn mã sau để tăng độ ổn định (Robustness).

Yêu cầu:

1. Giữ nguyên nghiệp vụ hiện tại.
2. Bổ sung kiểm tra null và chuỗi rỗng cho các tham số đầu vào.
3. Sử dụng try-with-resources thay cho việc đóng tài nguyên thủ công.
4. Xử lý IOException an toàn.
5. Xử lý NumberFormatException khi parse số tiền.
6. Nếu một dòng dữ liệu bị lỗi thì ghi nhận lỗi và bỏ qua dòng đó, không làm sập toàn bộ chương trình.
7. Không sử dụng catch(Exception).
8. Giải thích từng thay đổi đã thực hiện.

## Vòng 2 — Clean Code & SRP

Tiếp tục refactor kết quả ở vòng trước.

Yêu cầu:

1. Áp dụng nguyên tắc Single Responsibility Principle (SRP).
2. Tách các nhiệm vụ thành các lớp hoặc phương thức riêng:

   * FileReaderService
   * ReportCalculationService
   * ReportWriterService
3. Đổi toàn bộ tên biến sang CamelCase có ý nghĩa nghiệp vụ rõ ràng.
4. Loại bỏ mã lặp.
5. Tạo DTO hoặc Model phù hợp nếu cần.
6. Giảm độ lồng nhau của các khối if.
7. Giải thích cách thiết kế mới giúp dễ bảo trì và kiểm thử hơn.

## Vòng 3 — Logging & Context Tuning

Tiếp tục refactor kết quả ở vòng trước theo chuẩn doanh nghiệp.

Yêu cầu:

1. Sử dụng Lombok @Slf4j.
2. Thay thế toàn bộ System.out.println bằng log.info(), log.warn(), log.error().
3. Đưa các giá trị hardcode ra tham số cấu hình.
4. Ngưỡng số tiền tối thiểu phải được truyền động qua constructor hoặc method parameter.
5. Viết code theo chuẩn Java 17.
6. Đảm bảo mã nguồn cuối cùng dễ mở rộng và dễ test.
7. Sinh toàn bộ mã nguồn hoàn chỉnh cuối cùng.

# 3. Mã nguồn Java hoàn chỉnh sau tối ưu

```java
import lombok.extern.slf4j.Slf4j;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.List;

record OrderRecord(
        String orderId,
        double amount,
        String status
) {
}

record ReportResult(
        double totalAmount,
        List<String> orderIds
) {
}

class FileReaderService {

    public List<OrderRecord> readOrders(Path inputFile) throws IOException {

        List<OrderRecord> orders = new ArrayList<>();

        try (BufferedReader reader = Files.newBufferedReader(inputFile)) {

            String line;

            while ((line = reader.readLine()) != null) {

                try {

                    String[] fields = line.split(",");

                    if (fields.length < 3) {
                        continue;
                    }

                    String orderId = fields[0].trim();
                    double amount = Double.parseDouble(fields[1].trim());
                    String status = fields[2].trim();

                    orders.add(
                            new OrderRecord(
                                    orderId,
                                    amount,
                                    status
                            )
                    );

                } catch (NumberFormatException ex) {

                    // bỏ qua dòng lỗi
                }
            }
        }

        return orders;
    }
}

class ReportCalculationService {

    public ReportResult generateReport(
            List<OrderRecord> orders,
            double minimumAmount
    ) {

        double totalAmount = 0;
        List<String> qualifyingOrderIds = new ArrayList<>();

        for (OrderRecord order : orders) {

            boolean completed =
                    "COMPLETED".equalsIgnoreCase(order.status());

            boolean validAmount =
                    order.amount() > minimumAmount;

            if (completed && validAmount) {

                totalAmount += order.amount();
                qualifyingOrderIds.add(order.orderId());
            }
        }

        return new ReportResult(
                totalAmount,
                qualifyingOrderIds
        );
    }
}

class ReportWriterService {

    public void writeReport(
            Path outputFile,
            ReportResult reportResult
    ) throws IOException {

        try (BufferedWriter writer =
                     Files.newBufferedWriter(outputFile)) {

            writer.write(
                    "Total: " + reportResult.totalAmount()
            );

            writer.newLine();

            for (String orderId : reportResult.orderIds()) {

                writer.write(
                        "Order ID: " + orderId
                );

                writer.newLine();
            }
        }
    }
}

@Slf4j
public class ReportGenerator {

    private final FileReaderService fileReaderService;
    private final ReportCalculationService calculationService;
    private final ReportWriterService writerService;

    public ReportGenerator() {
        this.fileReaderService = new FileReaderService();
        this.calculationService = new ReportCalculationService();
        this.writerService = new ReportWriterService();
    }

    public void generateReport(
            String inputFilePath,
            String outputFilePath,
            double minimumAmount
    ) {

        if (inputFilePath == null || inputFilePath.isBlank()) {
            throw new IllegalArgumentException(
                    "Input file path must not be blank"
            );
        }

        if (outputFilePath == null || outputFilePath.isBlank()) {
            throw new IllegalArgumentException(
                    "Output file path must not be blank"
            );
        }

        try {

            Path inputFile = Path.of(inputFilePath);
            Path outputFile = Path.of(outputFilePath);

            List<OrderRecord> orders =
                    fileReaderService.readOrders(inputFile);

            ReportResult reportResult =
                    calculationService.generateReport(
                            orders,
                            minimumAmount
                    );

            writerService.writeReport(
                    outputFile,
                    reportResult
            );

            log.info(
                    "Report generated successfully. Input={}, Output={}",
                    inputFilePath,
                    outputFilePath
            );

        } catch (IOException ex) {

            log.error(
                    "Error processing report files",
                    ex
            );
        }
    }
}
```