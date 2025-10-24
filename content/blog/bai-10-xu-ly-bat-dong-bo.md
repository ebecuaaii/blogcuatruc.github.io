---
title: "Xử lý bất đồng bộ với async/await – Viết code Fetch API gọn gàng hơn"
date: 2025-10-23
tags: ["JavaScript", "Async Await", "Fetch API", "Promise", "Web Development"]
summary: "Tìm hiểu cơ chế bất đồng bộ trong JavaScript và cách dùng async/await để viết code Fetch API dễ hiểu, sạch sẽ và hiệu quả hơn."
cover:
  image: "/images/javascript/async-await.jpg"
  alt: "Minh họa async await trong JavaScript"
---

## 1. Giới thiệu

Khi làm việc với Fetch API, ta thường thấy cú pháp như sau:

```javascript
fetch(url)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```
Cách viết này hoạt động tốt, nhưng khi chuỗi thao tác phức tạp (nhiều .then() lồng nhau) thì code dễ rối.
Giải pháp: async/await — cú pháp mới của ES2017 giúp xử lý Promise gọn gàng, dễ đọc như code đồng bộ.

---
## 2. Cách hoạt động của async/await

- `async` khai báo một hàm bất đồng bộ, hàm này luôn trả về Promise.
- `await` được dùng bên trong hàm async, giúp "tạm dừng" hàm cho đến khi Promise hoàn thành.

### Ví dụ:

```javascript
async function demo() {
  console.log("Bắt đầu...");
  const data = await fetch("https://jsonplaceholder.typicode.com/posts/1");
  const post = await data.json();
  console.log(post);
  console.log("Kết thúc!");
}

demo();
```

### Kết quả in ra theo thứ tự dễ đọc:

```
Bắt đầu...
{ id: 1, title: "...", body: "..." }
Kết thúc!
```

## 3. Gửi dữ liệu bằng Fetch (POST request)

Trong thực tế, bạn không chỉ "lấy dữ liệu" mà còn "gửi dữ liệu" từ form hoặc ứng dụng đến server.

Ví dụ sau minh họa cách gửi dữ liệu bài viết mới bằng `fetch()` kết hợp `async/await`.


```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>POST với Fetch API</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f7f9fc;
      padding: 20px;
    }
    form {
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
      max-width: 400px;
      margin-bottom: 30px;
    }
    input, textarea {
      width: 100%;
      margin-bottom: 10px;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 6px;
    }
    button {
      background: #3498db;
      color: white;
      border: none;
      padding: 10px 15px;
      border-radius: 6px;
      cursor: pointer;
    }
    button:hover { background: #2980b9; }
    .result {
      background: #eaf6ff;
      padding: 15px;
      border-radius: 8px;
      max-width: 400px;
    }
  </style>
</head>
<body>
  <h1>Tạo bài viết mới</h1>
  
  <form id="postForm">
    <input type="text" id="title" placeholder="Tiêu đề" required />
    <textarea id="body" placeholder="Nội dung" rows="4" required></textarea>
    <button type="submit">Gửi bài viết</button>
  </form>
  
  <div id="result" class="result"></div>
  
  <script src="script.js"></script>
</body>
</html>
```

### JavaScript (file script.js)

```javascript
const form = document.getElementById("postForm");
const result = document.getElementById("result");

form.addEventListener("submit", async (e) => {
  e.preventDefault();
  
  const newPost = {
    title: document.getElementById("title").value,
    body: document.getElementById("body").value,
    userId: 1
  };
  
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/posts", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify(newPost)
    });
    
    const data = await response.json();
    
    result.innerHTML = `
      <h3>Bài viết đã được gửi!</h3>
      <p><strong>ID:</strong> ${data.id}</p>
      <p><strong>Tiêu đề:</strong> ${data.title}</p>
      <p><strong>Nội dung:</strong> ${data.body}</p>
    `;
  } catch (error) {
    result.innerHTML = `<p style="color:red;">Lỗi gửi dữ liệu: ${error}</p>`;
  }
});
```

## 4. Phân tích hoạt động

| Bước | Mô tả                                                           |
| ---- | --------------------------------------------------------------- |
| 1    | Người dùng nhập tiêu đề và nội dung vào form                    |
| 2    | Khi bấm "Gửi bài viết", JS ngăn reload trang (preventDefault()) |
| 3    | fetch() gửi dữ liệu dạng JSON đến API                           |
| 4    | Server phản hồi lại dữ liệu đã gửi (có ID mới)                  |
| 5    | Kết quả hiển thị ngay trên giao diện                            |

## 5. Ưu điểm của async/await

- Dễ đọc – không cần .then() lồng nhau
- Xử lý lỗi tự nhiên với try...catch
- Phù hợp cho các tác vụ bất đồng bộ phức tạp (gọi API nối tiếp, upload file, chờ dữ liệu,…)

## 6. Tổng kết

| Khái niệm   | Ý nghĩa                                    |
| ----------- | ------------------------------------------ |
| async       | Định nghĩa hàm bất đồng bộ                 |
| await       | Tạm dừng cho đến khi Promise hoàn tất      |
| try...catch | Bắt lỗi dễ dàng hơn trong code bất đồng bộ |

async/await giúp Fetch API trở nên dễ dùng và trực quan hơn, là công cụ không thể thiếu trong các ứng dụng web hiện đại.

## Bài tập luyện tập

1. Sửa ví dụ trên để hiển thị thông báo "Đang gửi dữ liệu..." khi chờ phản hồi từ server.
2. Thêm nút "Làm mới danh sách bài viết" để gọi lại API và hiển thị bài mới nhất.
3. Kết hợp GET và POST để tạo trang blog mini với JavaScript.