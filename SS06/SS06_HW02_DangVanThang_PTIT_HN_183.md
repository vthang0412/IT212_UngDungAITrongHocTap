## 1. Tầm quan trọng của thứ tự ưu tiên tính toán trong nghiệp vụ tài chính

Trong các hệ thống E-Commerce, ERP, POS hoặc kế toán, thứ tự áp dụng các quy tắc tính tiền là cực kỳ quan trọng vì:

* Thuế VAT thường được tính trên giá trị hàng hóa sau khi đã áp dụng các khoản giảm giá hợp lệ.
* Coupon thường có điều kiện áp dụng dựa trên giá trị đơn hàng tại một thời điểm xác định.
* Chiết khấu thành viên thường được áp dụng sau các chương trình khuyến mãi chung.
* Phí vận chuyển miễn phí thường dựa trên giá trị đơn hàng sau giảm giá nhưng trước thuế.
* Chỉ cần đảo thứ tự hai bước có thể làm sai số tiền thanh toán, thất thoát doanh thu hoặc vi phạm quy định kế toán.

Ví dụ:

```text
VAT trước giảm giá:
1.000.000 × 10% = 100.000

VAT sau giảm giá 20%:
800.000 × 10% = 80.000
```

Sai thứ tự làm chênh lệch:

```text
100.000 - 80.000 = 20.000 VND
```

Do đó cần yêu cầu AI suy luận từng bước (Chain-of-Thought) thay vì nhảy thẳng đến kết quả.

---

## 2. Prompt CoT tối ưu

Bạn là một Senior Financial System Analyst và Java Backend Architect.

Nhiệm vụ của bạn là phân tích nghiệp vụ tính tiền của hệ thống thương mại điện tử SpeedyCart.

YÊU CẦU QUAN TRỌNG:

Không được nhảy thẳng đến kết quả.

Hãy suy luận tuần tự từng bước theo phương pháp Chain-of-Thought.

Mỗi bước phải giải thích:

* Tại sao bước đó được thực hiện tại thời điểm đó.
* Công thức được sử dụng.
* Giá trị đầu vào.
* Giá trị đầu ra.

QUY TẮC NGHIỆP VỤ

1. Tính tổng tiền gốc của giỏ hàng.
2. Áp dụng Product Discount.
3. Áp dụng Coupon Code.
4. Áp dụng Loyalty Discount.
5. Xác định Shipping Fee.
6. Tính VAT.
7. Tính tổng thanh toán cuối cùng.

CHI TIẾT

Product Discount:

* Áp dụng trực tiếp trên từng sản phẩm.

Coupon:

* Giảm theo giá trị coupon.
* Chỉ áp dụng nếu đơn hàng sau Product Discount >= 500.000 VND.
* Nếu coupon là phần trăm thì giới hạn tối đa 100.000 VND.

Loyalty Discount:

* Gold giảm 5%.
* Platinum giảm 10%.
* Tính trên số tiền sau coupon.

VAT:

* VAT = 10%.
* Tính trên tổng tiền sau tất cả chiết khấu.

Shipping Fee:

* Dưới hoặc bằng 10km: 30.000 VND.
* Từ km thứ 11 trở đi: mỗi km cộng thêm 5.000 VND.
* Miễn phí vận chuyển nếu tổng tiền sau chiết khấu và trước VAT > 1.000.000 VND.

NHIỆM VỤ PHÂN TÍCH

Bước 1:
Xác định thứ tự thực hiện đúng.

Bước 2:
Liệt kê công thức toán học cho từng bước.

Bước 3:
Giải thích tại sao không thể đổi thứ tự giữa các bước.

Bước 4:
Thực hiện Dry-run chi tiết cho dữ liệu:

* Sản phẩm A:

    * Giá gốc: 400.000
    * Số lượng: 2
    * Giảm giá trực tiếp: 10%

* Sản phẩm B:

    * Giá gốc: 300.000
    * Số lượng: 1
    * Không giảm giá

Coupon:

* Giảm trực tiếp 50.000

Khách hàng:

* Hạng Gold

Khoảng cách giao hàng:

* 12km

Hãy hiển thị toàn bộ phép tính trung gian.

Bước 5:
Sinh mã Java hoàn chỉnh:

* Class OrderCalculator
* Method calculateTotal(Order order)
* Sử dụng BigDecimal
* Không dùng double hoặc float
* Có comment giải thích từng bước
* Xử lý đầy đủ Product Discount, Coupon, Loyalty Discount, VAT và Shipping Fee

ĐỊNH DẠNG ĐẦU RA

1. Logic Analysis
2. Formula Definition
3. Dry-run Result
4. Final Amount
5. Java Source Code

---

## 3. Minh chứng chạy thực tế (mẫu phản hồi AI)

### Logic Analysis

#### Bước 1 - Tính tổng tiền gốc

```text
A = 400.000 × 2 = 800.000
B = 300.000 × 1 = 300.000

Tổng gốc = 1.100.000
```

---

#### Bước 2 - Product Discount

Sản phẩm A giảm 10%.

```text
Giảm A = 800.000 × 10%
        = 80.000

A sau giảm = 720.000
```

Sản phẩm B không giảm.

```text
B sau giảm = 300.000
```

Tổng sau Product Discount:

```text
720.000 + 300.000
= 1.020.000
```

---

#### Bước 3 - Coupon

Điều kiện:

```text
1.020.000 >= 500.000
```

Đủ điều kiện.

Áp dụng coupon:

```text
1.020.000 - 50.000
= 970.000
```

---

#### Bước 4 - Loyalty Discount

Khách hàng Gold giảm 5%.

```text
970.000 × 5%
= 48.500
```

Tiền sau Loyalty:

```text
970.000 - 48.500
= 921.500
```

---

#### Bước 5 - Shipping Fee

Khoảng cách:

```text
12km
```

Không đủ điều kiện miễn phí vận chuyển vì:

```text
921.500 < 1.000.000
```

Phí ship:

```text
10km đầu = 30.000

2km tiếp theo:
2 × 5.000 = 10.000
```

Tổng ship:

```text
40.000
```

---

#### Bước 6 - VAT

VAT tính trên tổng tiền hàng sau giảm giá.

```text
VAT = 921.500 × 10%
     = 92.150
```

---

### Dry-run Result

| Thành phần           |   Giá trị |
| -------------------- | --------: |
| Tổng gốc             | 1.100.000 |
| Product Discount     |   -80.000 |
| Sau Product Discount | 1.020.000 |
| Coupon               |   -50.000 |
| Sau Coupon           |   970.000 |
| Loyalty Gold 5%      |   -48.500 |
| Sau Loyalty          |   921.500 |
| Shipping Fee         |   +40.000 |
| VAT 10%              |   +92.150 |
| Tổng thanh toán      | 1.053.650 |

---

### Final Amount

```text
1.053.650 VND
```

---

### Java Source Code

```java
import java.math.BigDecimal;
import java.math.RoundingMode;

public class OrderCalculator {

    private static final BigDecimal VAT_RATE =
            new BigDecimal("0.10");

    private static final BigDecimal GOLD_DISCOUNT =
            new BigDecimal("0.05");

    private static final BigDecimal PLATINUM_DISCOUNT =
            new BigDecimal("0.10");

    private static final BigDecimal FREE_SHIPPING_THRESHOLD =
            new BigDecimal("1000000");

    public BigDecimal calculateTotal(Order order) {

        // 1. Tổng tiền sau Product Discount
        BigDecimal subtotal =
                calculateSubtotalAfterProductDiscount(order);

        // 2. Coupon
        BigDecimal afterCoupon = subtotal;

        if (subtotal.compareTo(new BigDecimal("500000")) >= 0) {
            afterCoupon =
                    afterCoupon.subtract(order.getCouponAmount());
        }

        // 3. Loyalty Discount
        BigDecimal loyaltyDiscount = BigDecimal.ZERO;

        if ("GOLD".equalsIgnoreCase(order.getMemberLevel())) {
            loyaltyDiscount =
                    afterCoupon.multiply(GOLD_DISCOUNT);
        } else if ("PLATINUM".equalsIgnoreCase(order.getMemberLevel())) {
            loyaltyDiscount =
                    afterCoupon.multiply(PLATINUM_DISCOUNT);
        }

        BigDecimal afterLoyalty =
                afterCoupon.subtract(loyaltyDiscount);

        // 4. Shipping Fee
        BigDecimal shippingFee =
                calculateShippingFee(
                        afterLoyalty,
                        order.getDistanceKm()
                );

        // 5. VAT
        BigDecimal vat =
                afterLoyalty.multiply(VAT_RATE);

        // 6. Final Total
        return afterLoyalty
                .add(shippingFee)
                .add(vat)
                .setScale(0, RoundingMode.HALF_UP);
    }

    private BigDecimal calculateSubtotalAfterProductDiscount(
            Order order) {

        BigDecimal total = BigDecimal.ZERO;

        for (OrderItem item : order.getItems()) {

            BigDecimal itemTotal =
                    item.getPrice()
                            .multiply(
                                    BigDecimal.valueOf(
                                            item.getQuantity()
                                    )
                            );

            BigDecimal discount =
                    itemTotal.multiply(
                            item.getDiscountPercent()
                    );

            total = total.add(
                    itemTotal.subtract(discount)
            );
        }

        return total;
    }

    private BigDecimal calculateShippingFee(
            BigDecimal amountAfterDiscount,
            int distanceKm) {

        if (amountAfterDiscount.compareTo(
                FREE_SHIPPING_THRESHOLD) > 0) {
            return BigDecimal.ZERO;
        }

        BigDecimal fee =
                new BigDecimal("30000");

        if (distanceKm > 10) {
            fee = fee.add(
                    BigDecimal.valueOf(distanceKm - 10)
                            .multiply(
                                    new BigDecimal("5000")
                            )
            );
        }

        return fee;
    }
}
```

Lưu ý nghiệp vụ: trong thực tế doanh nghiệp cần thống nhất rõ VAT được tính trước hay sau phí vận chuyển. Trong đề bài này VAT được tính trên tổng tiền hàng sau chiết khấu, còn phí vận chuyển được cộng riêng sau đó. Đây là giả định đã được áp dụng trong phần dry-run trên.