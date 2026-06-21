## 1. Prompt sau khi tối ưu

Bạn hãy đóng vai một Giảng viên Java giàu kinh nghiệm, có khả năng giảng dạy trực quan và giúp sinh viên ghi nhớ kiến thức nhanh chóng.

Dựa trên đoạn tài liệu về Garbage Collection (GC) trong Java được cung cấp, hãy thực hiện các yêu cầu sau:

1. Tóm tắt nội dung dưới dạng bảng so sánh giữa Young Generation và Old Generation.
2. Bảng phải có các tiêu chí:

   * Các vùng con bên trong
   * Loại đối tượng lưu trữ
   * Điều kiện kích hoạt GC
   * Tốc độ thực thi của GC
3. Giải thích ngắn gọn, dễ hiểu, phù hợp cho sinh viên mới học Java.
4. Cung cấp thêm một sơ đồ ASCII trực quan mô tả đường đi của một đối tượng từ khi được tạo bằng từ khóa new cho đến khi được thăng cấp (Promoted) sang Old Generation.
5. Chỉ sử dụng thông tin có trong tài liệu được cung cấp, không tự bổ sung kiến thức bên ngoài.
6. Trình bày kết quả bằng Markdown rõ ràng, gồm:

   * Tiêu đề
   * Bảng so sánh
   * Sơ đồ ASCII
   * Phần ghi nhớ nhanh (Key Takeaways) tối đa 5 ý.

Tài liệu:
[Đặt đoạn văn bản GC vào đây]

---

## 2. Phản hồi từ AI sau khi chạy thử Prompt tối ưu

```markdown
# Tóm tắt Java Garbage Collection (GC)

## Bảng so sánh Young Generation và Old Generation

| Tiêu chí | Young Generation | Old Generation |
|-----------|------------------|----------------|
| Các vùng con bên trong | Eden Space, Survivor S0, Survivor S1 | Không đề cập vùng con trong tài liệu |
| Loại đối tượng lưu trữ | Đối tượng mới tạo, đối tượng có thời gian sống ngắn | Đối tượng có thời gian sống dài |
| Điều kiện kích hoạt GC | Khi Eden Space đầy sẽ kích hoạt Minor GC | Khi Old Generation đầy sẽ kích hoạt Major GC hoặc Full GC |
| Tốc độ thực thi của GC | Nhanh hơn | Chậm hơn đáng kể |
| Phạm vi dọn dẹp | Chủ yếu ở Young Generation | Có thể duyệt toàn bộ Heap |
| Ảnh hưởng đến ứng dụng | Ít tốn thời gian hơn | Có thể gây Stop-the-World |

---

## Sơ đồ ASCII mô tả vòng đời của đối tượng

          new
           |
           v
      +---------+
      |  Eden   |
      +---------+
           |
           | Eden đầy
           v
      Minor GC
           |
           v
   +---------------+
   | Survivor S0   |
   +---------------+
           |
      sống sót
           |
           v
   +---------------+
   | Survivor S1   |
   +---------------+
           |
      sống sót qua
   nhiều lần Minor GC
           |
           v
   +---------------+
   | Old Generation|
   +---------------+
           |
      Old đầy
           |
           v
    Major / Full GC

---

## Ghi nhớ nhanh (Key Takeaways)

1. Đối tượng mới được tạo bằng new sẽ nằm trong Eden Space.
2. Khi Eden đầy, Minor GC sẽ được kích hoạt.
3. Đối tượng còn sống sau Minor GC được chuyển sang Survivor.
4. Sau nhiều lần sống sót, đối tượng được Promote sang Old Generation.
5. Full GC chậm hơn Minor GC và có thể làm dừng toàn bộ ứng dụng (Stop-the-World).
```

### Vì sao prompt này tốt hơn prompt thô?

* Có **Role**: Giảng viên Java giàu kinh nghiệm.
* Có **Goal**: Tóm tắt và hệ thống hóa kiến thức.
* Có **Context**: Tài liệu GC được cung cấp.
* Có **Constraint**: Chỉ dùng thông tin trong tài liệu, bắt buộc so sánh theo các tiêu chí cho trước.
* Có **Format**: Bảng Markdown + sơ đồ ASCII + Key Takeaways.

Nhờ vậy AI sẽ trả về nội dung có cấu trúc, trực quan và dễ ghi nhớ hơn rất nhiều so với câu lệnh đơn giản: *"Tóm tắt cơ chế Garbage Collection trong Java từ đoạn văn trên"*.