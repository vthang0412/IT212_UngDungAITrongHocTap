## 1. Phân tích lỗ hổng của Prompt cũ

Prompt cũ:

> **"Giải thích hàm này làm gì và thêm comment vào code giúp tôi: [Đoạn code]"**

### Lỗ hổng

* Không chỉ định **vai trò** của AI (Senior Java Developer, Software Architect...).
* Không nêu **mục tiêu** rõ ràng (dịch ngược nghiệp vụ, bảo trì, chống regression).
* Không cung cấp **ngữ cảnh dự án**, khiến AI chỉ giải thích theo cú pháp thay vì nghiệp vụ.
* Không map ý nghĩa của các biến (`b`, `c`...), nên AI phải tự suy đoán.
* Không yêu cầu chuẩn **Javadoc**.
* Không yêu cầu comment chỉ đặt ở những đoạn logic khó.
* Không yêu cầu giữ nguyên logic chương trình.
* Không quy định định dạng đầu ra nên AI dễ trả lời lan man.

---

## 2. Prompt tối ưu

````markdown
# Vai trò (Role)

Bạn là Senior Java Developer với hơn 15 năm kinh nghiệm bảo trì hệ thống Enterprise Java và chuyên Reverse Engineering source code legacy.

# Mục tiêu (Objective)

Phân tích đoạn code Java dưới đây để:

- Dịch ngược logic nghiệp vụ.
- Giải thích mục đích của từng biến.
- Sinh Javadoc chuẩn.
- Thêm comment inline ở các đoạn tính toán hoặc điều kiện khó hiểu.
- Tuyệt đối không thay đổi logic chương trình.

# Ngữ cảnh (Context)

Đây là source code của hệ thống tính chiết khấu trong một dự án thương mại điện tử.

Ý nghĩa nghiệp vụ đã được xác định:

- b = loại khách hàng
    - b = 1 → Silver Member
    - b = 2 → Gold Member
    - giá trị khác → Normal Customer

- c = có Voucher Freeship
    - true → Có voucher
    - false → Không có voucher

Developer cũ đã đặt tên biến rất khó hiểu và không có bất kỳ comment nào.

Mục tiêu là giúp đội bảo trì hiểu rõ nghiệp vụ để tránh Regression Bug.

# Ràng buộc (Constraints)

- Không được thay đổi bất kỳ logic nào.
- Không đổi tên biến.
- Không tối ưu code.
- Chỉ bổ sung:
    - Javadoc
    - Comment inline
    - Giải thích nghiệp vụ
- Nếu phát hiện logic có khả năng gây lỗi thì ghi chú bằng TODO.
- Giải thích rõ từng điều kiện if.

# Định dạng đầu ra (Output Format)

Trả lời theo đúng thứ tự:

1. Tóm tắt nghiệp vụ của hàm.

2. Bảng mapping biến.

Ví dụ:

| Biến | Ý nghĩa |
|------|----------|
| a | Tổng tiền |
| b | Loại khách hàng |
| c | Voucher Freeship |

3. Mô tả từng bước xử lý của thuật toán.

4. Mã nguồn Java đã được bổ sung:
- Javadoc chuẩn
- Comment inline
- Không thay đổi logic

Đây là đoạn code:

```java
public double cD(double a,int b,boolean c){
    double d=0;
    if(b==1)d=a*0.05;
    else if(b==2)d=a*0.1;
    if(c)d+=30000;
    return d;
}
```
````

---

## 3. Kết quả AI sinh ra

### Giải thích nghiệp vụ

Hàm `cD()` dùng để tính tổng giá trị ưu đãi dành cho khách hàng.

Quy trình xử lý:

* Nếu khách hàng thuộc hạng Silver thì được giảm 5% giá trị đơn hàng.
* Nếu khách hàng thuộc hạng Gold thì được giảm 10% giá trị đơn hàng.
* Nếu khách hàng có Voucher Freeship thì được cộng thêm 30.000 VNĐ giá trị ưu đãi.
* Hàm trả về tổng giá trị khuyến mại.

---

### Mapping biến

| Biến | Ý nghĩa                       |
| ---- | ----------------------------- |
| a    | Tổng giá trị đơn hàng         |
| b    | Loại khách hàng               |
| c    | Có Voucher Freeship hay không |
| d    | Tổng giá trị ưu đãi           |

---

### Luồng xử lý

1. Khởi tạo tổng ưu đãi bằng 0.
2. Kiểm tra loại khách hàng.
3. Nếu Silver thì giảm 5%.
4. Nếu Gold thì giảm 10%.
5. Nếu có Voucher Freeship thì cộng thêm 30.000 VNĐ.
6. Trả về tổng ưu đãi.

---

### Mã nguồn Java đã bổ sung Javadoc và comment

```java
/**
 * Tính tổng giá trị ưu đãi dành cho khách hàng.
 *
 * <p>Quy tắc nghiệp vụ:</p>
 * <ul>
 *     <li>Silver Member (b = 1): giảm 5% giá trị đơn hàng.</li>
 *     <li>Gold Member (b = 2): giảm 10% giá trị đơn hàng.</li>
 *     <li>Nếu có Voucher Freeship (c = true): cộng thêm 30.000 VNĐ.</li>
 * </ul>
 *
 * @param a Tổng giá trị đơn hàng.
 * @param b Loại khách hàng.
 *          <ul>
 *              <li>1 = Silver Member</li>
 *              <li>2 = Gold Member</li>
 *              <li>Khác = Normal Customer</li>
 *          </ul>
 * @param c Trạng thái Voucher Freeship.
 *          true = Có voucher, false = Không có voucher.
 * @return Tổng giá trị ưu đãi khách hàng được hưởng.
 */
public double cD(double a, int b, boolean c) {

    // Khởi tạo tổng giá trị ưu đãi.
    double d = 0;

    // Nếu khách hàng là Silver Member thì được giảm 5%.
    if (b == 1)
        d = a * 0.05;

    // Nếu khách hàng là Gold Member thì được giảm 10%.
    else if (b == 2)
        d = a * 0.1;

    // Nếu có Voucher Freeship thì cộng thêm 30.000 VNĐ.
    if (c)
        d += 30000;

    // TODO:
    // Xác nhận lại với BA xem Voucher Freeship có được cộng đồng thời
    // với chiết khấu thành viên trong mọi trường hợp hay không.

    // Trả về tổng giá trị ưu đãi.
    return d;
}
```