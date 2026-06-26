## Đáp án: **B**

Phương án **B** là prompt tối ưu nhất vì nó mô tả đầy đủ yêu cầu kỹ thuật, ràng buộc nghiệp vụ, định nghĩa dữ liệu đầu vào/đầu ra, cơ chế xử lý lỗi và còn bổ sung kỹ thuật **Dry-run Chain of Thought (CoT)** để buộc AI kiểm chứng logic trước khi sinh mã nguồn.

---

# 1. Phân tích theo 4 cấu phần kỹ thuật

## 1. Input (Đầu vào)

### Phương án B

Xác định rõ:

* Input là chuỗi XML.
* Cấu trúc XML gồm:

```xml
<transaction>
    <id>TX001</id>
    <amount>100</amount>
    <status>SUCCESS</status>
</transaction>
```

AI biết chính xác:

* Dữ liệu cần parse.
* Các field cần đọc.
* Không phải suy đoán cấu trúc XML.

=> Giảm nguy cơ hallucination.

---

## 2. Processing (Xử lý)

### Phương án B

Mô tả rõ logic:

* Parse XML bằng DOM hoặc SAX.
* Validate:

```text
id != null
id != ""
amount > 0
```

* Nếu vi phạm:

```java
throw new InvalidTransactionException(...)
```

* Nếu XML lỗi cú pháp:

```java
ParserConfigurationException
SAXException
XMLStreamException
```

phải được xử lý.

=> AI có đầy đủ quy tắc nghiệp vụ.

---

## 3. Output (Đầu ra)

### Phương án B

Xác định rõ:

```java
List<TransactionDTO>
```

và

```java
public record TransactionDTO(
    String id,
    double amount,
    String status
) {}
```

AI không cần tự suy diễn kiểu dữ liệu.

---

## 4. Language / Environment

### Phương án B

Chỉ rõ:

```text
Java 17
```

và

```text
XML Parser tích hợp sẵn của Java
```

Do đó AI:

* Không dùng thư viện lạ.
* Không phụ thuộc Spring.
* Không phụ thuộc Jackson XML.

Đảm bảo code chạy được trong môi trường Java 17 thuần.

---

# 2. Vai trò của Dry-run CoT

Đây là điểm mạnh nhất của phương án B.

Prompt yêu cầu:

```text
1. Viết pseudocode.
2. Dry-run với XML lỗi.
3. Sau đó mới sinh code.
```

Ví dụ:

XML lỗi:

```xml
<transaction>
    <id>TX001
    <amount>100</amount>
</transaction>
```

thiếu:

```xml
</id>
```

Trong bước Dry-run AI sẽ phát hiện:

```text
DOM Parser
↓
parse()
↓
SAXException
↓
catch
↓
throw InvalidTransactionException
```

Nhờ vậy:

* Logic được kiểm tra trước.
* Giảm khả năng sinh code sai.
* Tăng độ tin cậy.

Đây chính là kỹ thuật Prompt Engineering hiện đại:

```text
Reason First
Code Later
```

---

# 3. Vì sao loại A?

Prompt A:

```text
Viết hộ tôi một hàm Java đọc chuỗi XML giao dịch và trả về danh sách TransactionDTO. Hãy bắt các lỗi dữ liệu.
```

Quá mơ hồ.

Không mô tả:

* XML có cấu trúc gì.
* TransactionDTO gồm field nào.
* Điều kiện hợp lệ là gì.
* Ngoại lệ nào cần ném.
* Java version.
* Parser nào.

AI có thể sinh:

```java
try {
   ...
} catch(Exception e){
}
```

hoặc:

```java
if(amount < 0)
```

thay vì:

```java
amount <= 0
```

=> Dễ phát sinh lỗi logic.

---

# 4. Vì sao loại C?

Prompt C:

```text
Sử dụng Stream API song song
Spring XML Reader
bỏ qua dòng lỗi
lưu thẳng vào database
```

Sai lệch yêu cầu bài toán.

Yêu cầu thực tế:

```text
Parse XML
Validate
Throw InvalidTransactionException
Return List<TransactionDTO>
```

Nhưng C lại:

* Thêm Spring.
* Thêm database.
* Thêm parallel processing.
* Bỏ qua giao dịch lỗi.

Nguy hiểm nhất:

```text
bỏ qua các dòng lỗi
```

Trong đề yêu cầu:

```java
throw InvalidTransactionException
```

=> Vi phạm nghiệp vụ.

Do đó C là lựa chọn kém nhất.

---

# Mã nguồn Java hoàn chỉnh

```java
import org.w3c.dom.*;
import org.xml.sax.SAXException;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import java.io.ByteArrayInputStream;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.List;

public class TransactionXmlParser {

    public record TransactionDTO(
            String id,
            double amount,
            String status
    ) {
    }

    public static class InvalidTransactionException extends Exception {
        public InvalidTransactionException(String message) {
            super(message);
        }

        public InvalidTransactionException(String message, Throwable cause) {
            super(message, cause);
        }
    }

    public static List<TransactionDTO> parseTransactions(String xml)
            throws InvalidTransactionException {

        try {
            DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
            factory.setNamespaceAware(true);

            DocumentBuilder builder = factory.newDocumentBuilder();

            Document document = builder.parse(
                    new ByteArrayInputStream(
                            xml.getBytes(StandardCharsets.UTF_8)
                    )
            );

            NodeList transactionNodes =
                    document.getElementsByTagName("transaction");

            List<TransactionDTO> result = new ArrayList<>();

            for (int i = 0; i < transactionNodes.getLength(); i++) {

                Element transaction =
                        (Element) transactionNodes.item(i);

                String id =
                        getTagValue(transaction, "id");

                String amountText =
                        getTagValue(transaction, "amount");

                String status =
                        getTagValue(transaction, "status");

                if (id == null || id.isBlank()) {
                    throw new InvalidTransactionException(
                            "Transaction id cannot be empty"
                    );
                }

                double amount;

                try {
                    amount = Double.parseDouble(amountText);
                } catch (NumberFormatException e) {
                    throw new InvalidTransactionException(
                            "Invalid amount format: " + amountText,
                            e
                    );
                }

                if (amount <= 0) {
                    throw new InvalidTransactionException(
                            "Amount must be greater than 0"
                    );
                }

                result.add(
                        new TransactionDTO(
                                id,
                                amount,
                                status
                        )
                );
            }

            return result;

        } catch (ParserConfigurationException | SAXException e) {
            throw new InvalidTransactionException(
                    "Invalid XML format",
                    e
            );
        } catch (Exception e) {
            if (e instanceof InvalidTransactionException) {
                throw (InvalidTransactionException) e;
            }

            throw new InvalidTransactionException(
                    "Unexpected parsing error",
                    e
            );
        }
    }

    private static String getTagValue(
            Element parent,
            String tagName
    ) {

        NodeList nodes =
                parent.getElementsByTagName(tagName);

        if (nodes.getLength() == 0) {
            return null;
        }

        return nodes.item(0)
                .getTextContent()
                .trim();
    }

    public static void main(String[] args) {

        String xml = """
                <transactions>
                    <transaction>
                        <id>TX001</id>
                        <amount>150.5</amount>
                        <status>SUCCESS</status>
                    </transaction>
                    <transaction>
                        <id>TX002</id>
                        <amount>200</amount>
                        <status>PENDING</status>
                    </transaction>
                </transactions>
                """;

        try {
            List<TransactionDTO> transactions =
                    parseTransactions(xml);

            transactions.forEach(System.out::println);

        } catch (InvalidTransactionException e) {
            System.err.println(e.getMessage());
        }
    }
}
```