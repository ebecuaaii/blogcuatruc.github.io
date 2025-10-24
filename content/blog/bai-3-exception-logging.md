---
title: "Xử lý ngoại lệ và Logging trong Java"
date: 2025-10-23
tags: ["Java", "Exception", "Logging", "Debug"]
summary: "Tìm hiểu cách Java quản lý lỗi bằng cơ chế Exception, cách sử dụng try-catch-finally, tạo Exception tùy chỉnh và ghi log với java.util.logging."
cover:
  image: "/images/java/java-exception-logging.jpg"
  alt: "Minh họa cơ chế Exception và Logging trong Java"
---

##  Giới thiệu

Không có chương trình nào **chạy mãi mà không lỗi**. Dù bạn viết Java backend, ứng dụng mạng, hay app desktop, sớm muộn gì cũng sẽ có lỗi xảy ra — **file không tồn tại, mất kết nối mạng, chia cho 0, lỗi định dạng dữ liệu…**

Đó là lý do Java cung cấp cơ chế **Exception Handling** (xử lý ngoại lệ) để giúp chương trình:
- **Không bị crash đột ngột**
- **Thông báo lỗi có ý nghĩa**
- **Ghi lại log để debug sau này**

---

##  Phân loại Exception trong Java

| Nhóm                    | Đặc điểm                           | Ví dụ                                                    |
| ----------------------- | ---------------------------------- | -------------------------------------------------------- |
| **Checked Exception**   | Bắt buộc phải xử lý (compile-time) | `IOException`, `SQLException`                            |
| **Unchecked Exception** | Không bắt buộc (runtime)           | `NullPointerException`, `ArrayIndexOutOfBoundsException` |
| **Error**               | Không nên xử lý trong code         | `OutOfMemoryError`, `StackOverflowError`                 |

📘 Checked Exception thường dùng cho các tình huống **ngoại cảnh** (mất file, mất mạng).  
Unchecked Exception là do **lỗi lập trình logic** (chia 0, null, index sai…).

---

##  Cấu trúc cơ bản try - catch - finally

Cú pháp:

```java
try {
    // Mã có thể gây lỗi
} catch (ExceptionType e) {
    // Xử lý lỗi
} finally {
    // Luôn chạy dù có lỗi hay không
}
```
Ví dụ :
```java
public class ExampleTryCatch {
    public static void main(String[] args) {
        try {
            int x = 10 / 0; // lỗi chia cho 0
            System.out.println("Không bao giờ in tới dòng này");
        } catch (ArithmeticException e) {
            System.err.println("Lỗi: Chia cho 0 không hợp lệ!");
        } finally {
            System.out.println("Luôn chạy khối finally.");
        }
    }
}
```
Kết quả:
```text
Lỗi: Chia cho 0 không hợp lệ!
Luôn chạy khối finally.
```
---
##  Xử lý nhiều ngoại lệ

Bạn có thể bắt nhiều loại lỗi khác nhau trong cùng khối try:

```java
try {
    int[] arr = {1, 2, 3};
    System.out.println(arr[5]); // lỗi IndexOutOfBounds
    int x = 10 / 0;              // lỗi Arithmetic
} catch (ArrayIndexOutOfBoundsException e) {
    System.err.println("Truy cập mảng sai vị trí!");
} catch (ArithmeticException e) {
    System.err.println("Lỗi chia cho 0!");
} catch (Exception e) {
    System.err.println("Lỗi không xác định: " + e.getMessage());
}
```

 **Lưu ý**: luôn đặt `catch (Exception e)` cuối cùng, vì nó bao hàm tất cả lỗi con.

##  Ném và tạo Exception tùy chỉnh

#### Dùng throw để ném lỗi thủ công:

```java
public void checkAge(int age) {
    if (age < 18)
        throw new IllegalArgumentException("Chưa đủ 18 tuổi!");
    System.out.println("Đủ tuổi truy cập.");
}
```

#### Tạo Exception riêng cho ứng dụng:

```java
public class InvalidDataException extends Exception {
    public InvalidDataException(String message) {
        super(message);
    }
}
```

#### Sử dụng:

```java
try {
    validate("abc");
} catch (InvalidDataException e) {
    System.err.println("Dữ liệu không hợp lệ: " + e.getMessage());
}
```

##  Logging trong Java (java.util.logging)

Khi ứng dụng lớn, bạn không thể chỉ `System.out.println()` để xem lỗi. Thay vào đó, dùng logging framework để:

- Ghi log ra file, console, hoặc server
- Gắn cấp độ log: INFO, WARNING, SEVERE, v.v.

### Ví dụ đơn giản:

```java
import java.util.logging.*;

public class LoggingDemo {
    private static final Logger logger = Logger.getLogger(LoggingDemo.class.getName());
    
    public static void main(String[] args) {
        logger.info("Ứng dụng bắt đầu...");
        try {
            int x = 10 / 0;
        } catch (ArithmeticException e) {
            logger.log(Level.SEVERE, "Lỗi toán học xảy ra", e);
        }
        logger.warning("Ứng dụng kết thúc với cảnh báo.");
    }
}
```

### Kết quả (console):

```
Thg 10 23, 2025 10:15:00 SA LoggingDemo main
INFO: Ứng dụng bắt đầu...
Thg 10 23, 2025 10:15:00 SA LoggingDemo main
SEVERE: Lỗi toán học xảy ra
java.lang.ArithmeticException: / by zero
...
WARNING: Ứng dụng kết thúc với cảnh báo.
```

##  Ghi log ra file

```java
try {
    FileHandler fh = new FileHandler("app.log", true);
    logger.addHandler(fh);
    fh.setFormatter(new SimpleFormatter());
    
    logger.info("Ghi log ra file app.log thành công!");
} catch (Exception e) {
    e.printStackTrace();
}
```

 Sau khi chạy, bạn sẽ thấy file `app.log` chứa toàn bộ lịch sử log của ứng dụng.

##  Cấp độ Log phổ biến

| Cấp độ                | Ý nghĩa                         |
| --------------------- | ------------------------------- |
| SEVERE                | Lỗi nghiêm trọng, cần can thiệp |
| WARNING               | Cảnh báo tiềm ẩn                |
| INFO                  | Thông tin chung                 |
| CONFIG                | Thông tin cấu hình              |
| FINE / FINER / FINEST | Dùng khi debug chi tiết         |

##  Ví dụ tổng hợp: đọc file an toàn + log lỗi

```java
import java.io.*;
import java.util.logging.*;

public class FileReaderExample {
    private static final Logger logger = Logger.getLogger(FileReaderExample.class.getName());
    
    public static void main(String[] args) {
        try {
            FileHandler fh = new FileHandler("readfile.log", true);
            fh.setFormatter(new SimpleFormatter());
            logger.addHandler(fh);
            
            readFile("data.txt");
        } catch (IOException e) {
            logger.severe("Không thể tạo file log!");
        }
    }
    
    static void readFile(String fileName) {
        try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = br.readLine()) != null) {
                logger.info("Đọc dòng: " + line);
            }
        } catch (FileNotFoundException e) {
            logger.warning("Không tìm thấy file: " + fileName);
        } catch (IOException e) {
            logger.severe("Lỗi đọc file: " + e.getMessage());
        } finally {
            logger.info("Hoàn tất đọc file.");
        }
    }
}
```

Khi chạy, nếu file `data.txt` không tồn tại, bạn sẽ thấy log trong console và trong file `readfile.log`.

##  Best Practices

- Không nên để `catch (Exception e)` mà không xử lý gì.
- Ghi log có ngữ cảnh rõ ràng (`logger.log(Level.SEVERE, "Lỗi khi đọc file", e)`).
- Dùng framework logging chuyên nghiệp hơn khi dự án lớn: SLF4J, Log4j2, Lombok @Slf4j.
- Với ứng dụng mạng: log địa chỉ IP client, thời gian, loại request để dễ truy vết.

##  Bài tập gợi ý

1. Viết chương trình đăng nhập: nếu người dùng nhập sai mật khẩu 3 lần, ném Exception `TooManyAttemptsException`.
2. Tạo file `log.txt` và ghi log khi người dùng thực hiện thao tác (login, logout, thất bại).
3. Sử dụng try-with-resources để đảm bảo file luôn được đóng.

##  Tổng kết

- **Exception Handling** giúp chương trình không bị crash đột ngột.
- **Logging** giúp theo dõi lỗi và hoạt động hệ thống.
- Hai kỹ thuật này kết hợp là nền tảng cho mọi ứng dụng Java thực tế — đặc biệt trong lập trình mạng, khi lỗi kết nối và dữ liệu là chuyện thường xuyên.

## Bài tiếp theo

**"Lập trình Socket cơ bản trong Java"** — bạn sẽ học cách tạo client-server và gửi dữ liệu giữa hai máy tính.

