---
title: "Tạo server đa luồng bằng Java – Chat nhiều người như Messenger mini"
date: 2025-10-23
tags: ["Java", "Socket", "Thread", "Networking", "Chat App"]
summary: "Xây dựng server đa luồng cho phép nhiều client kết nối và trò chuyện cùng lúc – nền tảng của các ứng dụng chat như Messenger hoặc Zalo mini."
cover:
  image: "/images/java/java-multithreaded-chat-server.jpg"
  alt: "Server chat đa luồng trong Java với nhiều client kết nối"
---

#  Bài 5: Giao tiếp đa luồng trong lập trình mạng Java

##  Mục tiêu
Mở rộng ứng dụng mạng để cho phép **nhiều client** kết nối và trò chuyện **song song** với nhau.

---

##  Nội dung chính
- Giới thiệu `Thread` và `Runnable` trong Java  
- Tạo **Server đa luồng (Multi-threaded Server)**  
- Quản lý nhiều client cùng lúc  
- Minh họa: Ứng dụng **Chat mini** như Messenger  

---

##  1. Giới thiệu Thread & Runnable

Trong Java, mỗi **Thread** là một luồng xử lý riêng biệt.  
Khi có nhiều client cùng kết nối, ta dùng nhiều luồng để server **xử lý song song**, tránh tình trạng “nghẽn”.

**Ví dụ đơn giản:**
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Luồng đang chạy: " + Thread.currentThread().getName());
    }
}

public class DemoThread {
    public static void main(String[] args) {
        for (int i = 1; i <= 3; i++) {
            new MyThread().start();
        }
    }
}
```
**Kết quả:** Chương trình in ra 3 luồng chạy gần như cùng lúc.

---
## 2. Xây dựng Server đa luồng

### Ý tưởng

- Mỗi khi có client kết nối → Server tạo một luồng riêng để giao tiếp với client đó.
- Các client gửi tin nhắn, server sẽ phát lại (broadcast) cho tất cả những client khác.

### Server.java

```java
import java.io.*;
import java.net.*;
import java.util.*;

public class ChatServer {
    private static final int PORT = 8080;
    private static Set<PrintWriter> clientWriters = new HashSet<>();
    
    public static void main(String[] args) {
        System.out.println("Server chat đang chạy trên cổng " + PORT + "...");
        try (ServerSocket serverSocket = new ServerSocket(PORT)) {
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Kết nối mới từ: " + clientSocket.getInetAddress());
                new ClientHandler(clientSocket).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    static class ClientHandler extends Thread {
        private Socket socket;
        private PrintWriter out;
        private BufferedReader in;
        
        public ClientHandler(Socket socket) {
            this.socket = socket;
        }
        
        public void run() {
            try {
                in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                out = new PrintWriter(socket.getOutputStream(), true);
                
                synchronized (clientWriters) {
                    clientWriters.add(out);
                }
                
                out.println("Chào bạn! Nhập 'exit' để thoát.");
                
                String message;
                while ((message = in.readLine()) != null) {
                    if (message.equalsIgnoreCase("exit")) break;
                    System.out.println("Từ client: " + message);
                    broadcast("Người dùng: " + message);
                }
            } catch (IOException e) {
                System.out.println("Lỗi kết nối client.");
            } finally {
                try { socket.close(); } catch (IOException e) {}
                synchronized (clientWriters) {
                    clientWriters.remove(out);
                }
                System.out.println("Một client đã ngắt kết nối.");
            }
        }
        
        private void broadcast(String message) {
            synchronized (clientWriters) {
                for (PrintWriter writer : clientWriters) {
                    writer.println(message);
                }
            }
        }
    }
}
```

### Client.java

```java
import java.io.*;
import java.net.*;

public class ChatClient {
    public static void main(String[] args) {
        try (Socket socket = new Socket("localhost", 8080);
             BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
             PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
             BufferedReader userInput = new BufferedReader(new InputStreamReader(System.in))) {
            
            System.out.println("Đã kết nối tới server chat!");
            new Thread(() -> {
                try {
                    String serverMsg;
                    while ((serverMsg = in.readLine()) != null) {
                        System.out.println(serverMsg);
                    }
                } catch (IOException e) {
                    System.out.println("Mất kết nối tới server.");
                }
            }).start();
            
            String input;
            while ((input = userInput.readLine()) != null) {
                out.println(input);
                if (input.equalsIgnoreCase("exit")) break;
            }
        } catch (IOException e) {
            System.out.println("Không thể kết nối tới server.");
        }
    }
}
```

## 3. Cách chạy thử

1. Mở nhiều cửa sổ terminal (1 server + 2 hoặc 3 client).

2. Trong cửa sổ 1 (Server):
   ```bash
   javac ChatServer.java
   java ChatServer
   ```

3. Trong cửa sổ 2 (Client 1):
   ```bash
   javac ChatClient.java
   java ChatClient
   ```

4. Trong cửa sổ 3 (Client 2):
   ```bash
   java ChatClient
   ```

## Kết quả mẫu

### Server console:
```
Server chat đang chạy trên cổng 8080...
Kết nối mới từ: /127.0.0.1
Từ client: Xin chào!
Từ client: Ai đang ở đây không?
```

### Client 1:
```
Chào bạn! Nhập 'exit' để thoát.
Người dùng: Ai đang ở đây không?
Người dùng: Mình đây nè!
```

### Client 2:
```
Chào bạn! Nhập 'exit' để thoát.
Người dùng: Xin chào!
Người dùng: Ai đang ở đây không?
```

## 4. Phân tích hoạt động

| Bước | Thực hiện                | Mô tả                                       |
| ---- | ------------------------ | ------------------------------------------- |
| 1    | `new ServerSocket(8080)` | Mở cổng lắng nghe                           |
| 2    | `serverSocket.accept()`  | Chờ client kết nối                          |
| 3    | Mỗi client → new Thread  | Xử lý song song                             |
| 4    | `broadcast()`            | Gửi tin nhắn tới tất cả client đang kết nối |

## 5. Lưu ý & mở rộng

- Dùng `synchronized` để tránh xung đột khi nhiều luồng ghi dữ liệu.
- Có thể thêm tên người dùng (username) để hiển thị đẹp hơn.
- Có thể nâng cấp lên GUI Chat App với Swing hoặc JavaFX.
- Để server hỗ trợ ngắt kết nối mềm, cần thêm `socket.close()` đúng cách.

## Bài tập mở rộng

1. Thêm tính năng đặt tên người dùng khi client kết nối.
2. Ghi lại toàn bộ tin nhắn vào file `chat.log`.
3. Nâng cấp giao diện bằng Swing: tạo cửa sổ chat, danh sách người online.
4. Tạo "Private Chat" – gửi tin nhắn riêng cho từng client.

## Tổng kết

- `ServerSocket` lắng nghe kết nối.
- Mỗi client → một Thread xử lý riêng biệt.
- `synchronized` đảm bảo an toàn dữ liệu khi nhiều luồng truy cập.
- Đây là nền tảng của các ứng dụng chat đa người dùng, game online, hoặc hệ thống giao tiếp theo thời gian thực.

## Bài tiếp theo

"Giao tiếp Server – Client bằng Object Stream (truyền đối tượng trong Java)"
