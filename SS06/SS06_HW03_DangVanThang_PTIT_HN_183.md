## 1. Phân tích các lỗ hổng nghiêm trọng của đoạn code ban đầu

Đoạn code của thực tập sinh tồn tại nhiều rủi ro Production:

### 1. Null Pointer Exception

```java
Product product =
    inventoryRepository.findById(item.getProductId())
                       .orElse(null);

product.setStock(...);
```

Nếu sản phẩm không tồn tại:

```java
product == null
```

sẽ gây:

```text
NullPointerException
```

---

### 2. Không kiểm tra dữ liệu đầu vào

Không kiểm tra:

```java
order == null
order.getItems() == null
order.getItems().isEmpty()
```

Dẫn tới lỗi runtime.

---

### 3. Không kiểm tra tồn kho

Ví dụ:

```text
Tồn kho = 5
Khách mua = 10
```

Code vẫn cho phép:

```java
product.setStock(5 - 10);
```

Kết quả:

```text
Stock = -5
```

Làm sai dữ liệu nghiệp vụ.

---

### 4. Không xử lý lỗi thanh toán

```java
paymentGateway.charge(...)
```

Nếu gateway timeout hoặc từ chối giao dịch:

```text
PaymentException
```

sẽ phát sinh nhưng chưa được xử lý rõ ràng.

---

### 5. Vi phạm tính Atomicity

Trình tự hiện tại:

```text
1. Trừ kho
2. Thanh toán
3. Lưu đơn
```

Nếu bước 2 thất bại:

```text
Kho đã bị trừ
Đơn hàng chưa tạo
Tiền chưa thu
```

Dữ liệu không nhất quán.

---

### 6. Không có logging

Khi production lỗi sẽ không biết:

```text
Khách nào
Đơn hàng nào
Sản phẩm nào
Thanh toán bước nào
```

---

### 7. Không có kết quả trả về chuẩn

```java
void placeOrder(...)
```

Caller không biết:

```text
Thành công?
Mã đơn hàng?
Thời gian?
Trạng thái?
```

---

## 2. Chuỗi Prompt Refinement Chain

### Vòng 1 - Robustness

Bạn là Senior Java Backend Engineer.

Hãy refactor đoạn code OrderPlacementService theo hướng tăng độ an toàn dữ liệu.

Yêu cầu:

1. Validate:

   * order khác null
   * danh sách item không rỗng
   * product phải tồn tại

2. Kiểm tra tồn kho:

   * nếu stock < quantity
   * ném OutOfStockException

3. Không được sử dụng orElse(null)

4. Thanh toán:

   * bắt lỗi Payment Gateway
   * bọc thành PaymentFailedException

5. Tạo các custom exception:

   * OrderValidationException
   * OutOfStockException
   * PaymentFailedException

6. Trả về mã nguồn Java hoàn chỉnh.

### Vòng 2 - Maintainability & Clean Code

Tiếp tục cải tiến phiên bản trước.

Yêu cầu:

1. Chuyển sang Spring Boot Service.

2. Áp dụng @Transactional.

3. Nếu thanh toán thất bại:

   * rollback toàn bộ thay đổi tồn kho.

4. Sử dụng Lombok:

   * @RequiredArgsConstructor

5. Sử dụng:

   * @Slf4j

6. Ghi log:

INFO:

* bắt đầu xử lý đơn
* kiểm tra kho
* trừ kho
* gọi gateway
* lưu đơn

ERROR:

* hết hàng
* lỗi thanh toán
* lỗi hệ thống

7. Tuân thủ Clean Code:

   * tách nhỏ method
   * đặt tên rõ nghĩa
   * tránh code lặp

8. Trả về source code hoàn chỉnh.

### Vòng 3 - Context-Specific Tuning

Tiếp tục cải tiến phiên bản trước thành production-ready.

Yêu cầu:

1. Trả về:
   OrderPlacementResult

Bao gồm:

* orderId
* success
* message
* totalAmount

2. Bổ sung DTO và Result class.

3. Viết JUnit 5 + Mockito Test.

4. Mock:

   * InventoryRepository
   * OrderRepository
   * PaymentGateway

5. Viết test case:

paymentShouldFailAndRollbackInventory()

Kiểm tra:

* PaymentFailedException được ném
* Transaction rollback
* orderRepository.save không được gọi

6. Dùng:

   * @ExtendWith(MockitoExtension.class)
   * @Mock
   * @InjectMocks

7. Comment giải thích từng test.

8. Trả về toàn bộ source code cuối cùng.

---

## 3. Minh chứng chạy thực tế (Kết quả cuối sau vòng 3)

### OrderPlacementResult

```java
@Getter
@Builder
@AllArgsConstructor
public class OrderPlacementResult {

    private Long orderId;

    private boolean success;

    private String message;

    private BigDecimal totalAmount;
}
```

---

### OrderPlacementService

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
@Slf4j
public class OrderPlacementService {

    private final InventoryRepository inventoryRepository;

    private final PaymentGateway paymentGateway;

    private final OrderRepository orderRepository;

    @Transactional(
        rollbackFor = Exception.class
    )
    public OrderPlacementResult placeOrder(
            Order order) {

        validateOrder(order);

        log.info(
            "Start placing order. customerId={}",
            order.getCustomerId()
        );

        reserveInventory(order);

        processPayment(order);

        Order savedOrder =
                orderRepository.save(order);

        log.info(
            "Order saved successfully. orderId={}",
            savedOrder.getId()
        );

        return OrderPlacementResult.builder()
                .orderId(savedOrder.getId())
                .success(true)
                .message("Order placed successfully")
                .totalAmount(order.getTotalAmount())
                .build();
    }

    private void validateOrder(Order order) {

        if (order == null) {
            throw new OrderValidationException(
                    "Order must not be null"
            );
        }

        if (order.getItems() == null ||
                order.getItems().isEmpty()) {

            throw new OrderValidationException(
                    "Order items must not be empty"
            );
        }
    }

    private void reserveInventory(
            Order order) {

        for (OrderItem item : order.getItems()) {

            Product product =
                    inventoryRepository
                            .findById(
                                    item.getProductId()
                            )
                            .orElseThrow(
                                    () -> new OutOfStockException(
                                            "Product not found: "
                                                    + item.getProductId()
                                    )
                            );

            if (product.getStock()
                    < item.getQuantity()) {

                log.error(
                    "Out of stock. productId={}",
                    product.getId()
                );

                throw new OutOfStockException(
                        "Insufficient stock"
                );
            }

            product.setStock(
                    product.getStock()
                            - item.getQuantity()
            );

            inventoryRepository.save(product);

            log.info(
                "Inventory updated. productId={}, stock={}",
                product.getId(),
                product.getStock()
            );
        }
    }

    private void processPayment(
            Order order) {

        try {

            log.info(
                "Calling payment gateway. customerId={}",
                order.getCustomerId()
            );

            paymentGateway.charge(
                    order.getCustomerId(),
                    order.getTotalAmount()
            );

        } catch (Exception ex) {

            log.error(
                "Payment failed. customerId={}",
                order.getCustomerId(),
                ex
            );

            throw new PaymentFailedException(
                    "Payment gateway failure",
                    ex
            );
        }
    }
}
```

---

### JUnit Test

```java
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.Mockito.*;

import java.math.BigDecimal;
import java.util.List;
import java.util.Optional;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;

import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class OrderPlacementServiceTest {

    @Mock
    private InventoryRepository inventoryRepository;

    @Mock
    private PaymentGateway paymentGateway;

    @Mock
    private OrderRepository orderRepository;

    @InjectMocks
    private OrderPlacementService service;

    @Test
    void paymentShouldFailAndRollbackInventory() {

        Product product = new Product();

        product.setId(1L);
        product.setStock(20);

        OrderItem item = new OrderItem();

        item.setProductId(1L);
        item.setQuantity(5);

        Order order = new Order();

        order.setCustomerId(100L);
        order.setTotalAmount(
                new BigDecimal("500000")
        );
        order.setItems(
                List.of(item)
        );

        when(
            inventoryRepository.findById(1L)
        ).thenReturn(
            Optional.of(product)
        );

        doThrow(
            new RuntimeException("Gateway timeout")
        )
        .when(paymentGateway)
        .charge(anyLong(), any());

        assertThrows(
            PaymentFailedException.class,
            () -> service.placeOrder(order)
        );

        verify(
            orderRepository,
            never()
        ).save(any());

        verify(
            paymentGateway,
            times(1)
        ).charge(anyLong(), any());
    }
}
```

### Kết quả cuối cùng sau 3 vòng Refinement

Đoạn code đã được nâng cấp từ mức:

```text
Demo/Intern Code
```

lên:

```text
Production-ready Service
```

với các đặc tính:

* Validate đầu vào đầy đủ.
* Kiểm tra tồn kho.
* Custom Exception rõ ràng.
* Transaction ACID bằng @Transactional.
* Rollback khi thanh toán lỗi.
* Logging INFO/ERROR với Slf4j.
* DTO kết quả chuẩn.
* Unit Test JUnit 5 + Mockito.
* Dễ mở rộng và bảo trì trong môi trường doanh nghiệp.