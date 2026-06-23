# Đáp án: **Phương án B**

## 1. Vì sao phương án B là lựa chọn tối ưu nhất?

Mục tiêu của đề bài không phải chỉ là:

```text
Biết viết code AOP
```

mà là:

```text
Hiểu bản chất Spring AOP
↓
Hiểu các thuật ngữ
↓
Biết khi nào sử dụng
↓
Áp dụng được vào dự án thực tế
```

Phương án B đáp ứng đầy đủ 3 nguyên tắc học kỹ thuật nâng cao:

### 1. Level-based Explanation (Giải thích đa cấp độ)

### 2. Comparative Analysis (Phân tích so sánh)

### 3. Practical Examples (Ví dụ thực tiễn)

---

# 2. Phân tích theo các nguyên tắc học kỹ thuật nâng cao

## A. Level-based Explanation

Prompt B yêu cầu:

> Giải thích theo 2 cấp độ.

### Cấp độ 1 - Beginner

Ví dụ:

```text
Camera Filter
```

hoặc:

```text
Trạm kiểm soát giao thông
```

Người học sẽ hiểu:

```text
Logic chính = Xe chạy trên đường

AOP = Trạm kiểm tra gắn thêm
```

không cần sửa logic gốc.

---

### Cấp độ 2 - Senior Developer

Prompt yêu cầu giải thích:

* Aspect
* Pointcut
* JoinPoint
* Advice

Ví dụ:

```text
Aspect
=
Module chứa logic ghi log
```

```text
Pointcut
=
Nơi sẽ chèn log
```

```text
Advice
=
Đoạn code chạy trước/sau
```

Người học hiểu được cả:

```text
Tư duy nghiệp vụ
+
Cơ chế kỹ thuật
```

---

## B. Comparative Analysis

Prompt B yêu cầu:

> So sánh AOP và OOP

Đây là điểm rất quan trọng.

Nhiều người học AOP nhưng không hiểu:

```text
Tại sao OOP chưa đủ?
```

---

Ví dụ:

### OOP

```java
public void save() {
    log.info("start");
    ...
}
```

Mỗi method phải tự log.

---

### AOP

```java
@Before(...)
public void log() {
}
```

Log được chèn tự động.

---

Bảng so sánh giúp hiểu:

| OOP                  | AOP                                          |
| -------------------- | -------------------------------------------- |
| Xử lý nghiệp vụ      | Xử lý vấn đề cắt ngang                       |
| Đóng gói đối tượng   | Đóng gói hành vi phụ trợ                     |
| Tập trung vào domain | Tập trung vào logging, security, transaction |

---

## C. Practical Examples

Prompt B yêu cầu:

```java
@Aspect
@Before
@After
```

và:

```text
Có chú thích tiếng Việt
```

Điều này giúp chuyển từ:

```text
Biết lý thuyết
```

sang

```text
Biết áp dụng
```

---

# 3. Tại sao phương án B vượt trội hơn A và C?

## Loại A

### Prompt A

> Giải thích Spring AOP là gì và viết ví dụ.

### Nhược điểm

#### Quá ngắn

AI thường trả lời:

```text
Định nghĩa
↓
Ví dụ
```

---

#### Không có cấp độ giải thích

Người mới dễ gặp:

```text
Aspect
Pointcut
JoinPoint
Advice
```

nhưng không hiểu.

---

#### Không có so sánh

Không trả lời được:

```text
Tại sao phải dùng AOP?
```

---

#### Thiếu định hướng học tập

Chỉ biết:

```text
Làm như thế nào
```

không biết:

```text
Vì sao phải làm như vậy
```

---

# Loại C

### Prompt C

> Viết class Aspect và cấu hình pom.xml

### Nhược điểm

#### Tập trung code quá sớm

AI sẽ viết:

```java
@Aspect
@Component
```

ngay lập tức.

---

#### Không giải thích bản chất

Người học chỉ biết:

```java
Copy Paste
```

---

#### Không có tư duy kiến trúc

Không hiểu:

```text
Cross-cutting Concern
```

---

#### Không có so sánh OOP và AOP

Mất đi phần quan trọng nhất.

---

# 4. Ví dụ Aspect ghi log trong Spring Boot

## LoggingAspect.java

```java
package com.example.aop.aspect;

import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
@Slf4j
public class LoggingAspect {

    /**
     * Áp dụng cho tất cả method trong package service
     */
    @Before("execution(* com.example.aop.service.*.*(..))")
    public void beforeMethod(JoinPoint joinPoint) {

        String methodName = joinPoint.getSignature().getName();

        log.info("Bắt đầu thực thi method: {}", methodName);
    }

    @After("execution(* com.example.aop.service.*.*(..))")
    public void afterMethod(JoinPoint joinPoint) {

        String methodName = joinPoint.getSignature().getName();

        log.info("Kết thúc thực thi method: {}", methodName);
    }
}
```

---

## UserService.java

```java
package com.example.aop.service;

import org.springframework.stereotype.Service;

@Service
public class UserService {

    public void createUser() {

        System.out.println("Đang tạo người dùng...");
    }
}
```

---

## Kết quả khi gọi

```java
userService.createUser();
```

Log:

```text
Bắt đầu thực thi method: createUser
Đang tạo người dùng...
Kết thúc thực thi method: createUser
```