---
title: "Kết nối server bằng Fetch API – Cách JavaScript trò chuyện với backend"
date: 2025-10-23
tags: ["JavaScript", "Networking", "Fetch API", "JSON", "Web Development"]
summary: "Tìm hiểu cách JavaScript giao tiếp với server qua Fetch API, xử lý dữ liệu JSON và hiển thị nội dung động từ API công khai."
cover:
  image: "/images/javascript/fetch-api-json.jpg"
  alt: "Minh họa giao tiếp mạng bằng Fetch API và JSON"
---

## 1. Giới thiệu

Ngày nay, các ứng dụng web hiện đại không chỉ hiển thị nội dung tĩnh mà còn **trao đổi dữ liệu với server** để cập nhật thông tin liên tục.  
JavaScript cung cấp công cụ mạnh mẽ để thực hiện việc này – đó là **Fetch API**.

Fetch API cho phép bạn **gửi yêu cầu HTTP** (GET, POST, PUT, DELETE, …) và **nhận dữ liệu phản hồi** từ server, thường ở định dạng **JSON**.

---

## 2. Gửi và nhận dữ liệu bằng Fetch API

Cú pháp cơ bản:

```javascript
fetch(url, {
  method: "GET", // hoặc POST, PUT, DELETE...
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(data) // chỉ dùng khi gửi dữ liệu
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error("Lỗi:", error));
```

### Giải thích

- `fetch()` trả về Promise, cho phép xử lý bất đồng bộ.
- `response.json()` giúp chuyển phản hồi (response) từ server sang đối tượng JSON mà JS có thể sử dụng.
- `catch()` bắt lỗi nếu quá trình kết nối thất bại.

## 3. Làm việc với JSON

JSON (JavaScript Object Notation) là định dạng dữ liệu phổ biến nhất để trao đổi giữa frontend và backend.

### Ví dụ JSON mẫu:

```json
{
  "id": 1,
  "title": "Bài viết đầu tiên",
  "body": "Nội dung bài viết...",
  "userId": 1
}
```

JavaScript có thể chuyển đổi giữa chuỗi và đối tượng JSON bằng:

```javascript
const jsonString = JSON.stringify({ name: "Truc", age: 21 });
const obj = JSON.parse(jsonString);
```

## 4. Ví dụ: Gọi API công khai

Chúng ta sẽ thử gọi API `https://jsonplaceholder.typicode.com/posts`, đây là một API giả lập miễn phí chứa danh sách bài viết.

### HTML

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Fetch API Demo</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #fafafa;
      padding: 20px;
    }
    .post {
      background: white;
      margin-bottom: 15px;
      padding: 15px;
      border-radius: 10px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    h2 { color: #2c3e50; }
  </style>
</head>
<body>
  <h1>Danh sách bài viết</h1>
  <div id="post-list"></div>
  
  <script src="script.js"></script>
</body>
</html>
```

### JavaScript (file script.js)

```javascript
const postList = document.getElementById("post-list");

fetch("https://jsonplaceholder.typicode.com/posts")
  .then(response => response.json())
  .then(posts => {
    posts.slice(0, 10).forEach(post => {
      const div = document.createElement("div");
      div.className = "post";
      div.innerHTML = `
        <h2>${post.title}</h2>
        <p>${post.body}</p>
      `;
      postList.appendChild(div);
    });
  })
  .catch(error => {
    postList.innerHTML = `<p style="color:red;">Lỗi tải dữ liệu: ${error}</p>`;
  });
```

## 5. Kết quả minh họa

Khi chạy trang này, trình duyệt sẽ tự động gửi yêu cầu đến API và hiển thị 10 bài viết đầu tiên:

- **Bài viết 1:** sunt aut facere repellat provident occaecati excepturi optio reprehenderit
- **Bài viết 2:** qui est esse
- **Bài viết 3:** ea molestias quasi exercitationem repellat qui ipsa sit aut
- ...

Mỗi bài viết được hiển thị trong một khung riêng.

## 6. Tổng kết

| Khái niệm | Ý nghĩa                                          |
| --------- | ------------------------------------------------ |
| Fetch API | Gửi/nhận dữ liệu HTTP qua JavaScript             |
| Promise   | Giúp xử lý thao tác bất đồng bộ                  |
| JSON      | Định dạng dữ liệu phổ biến giữa client và server |

Fetch API là cầu nối giữa frontend và backend, giúp ứng dụng web trở nên động, phản hồi nhanh và thân thiện hơn với người dùng.

## 7. Bài tập mở rộng

1. Thêm chức năng hiển thị chi tiết bài viết khi click vào tiêu đề.
2. Viết thêm form gửi dữ liệu POST để tạo bài viết mới lên API.
3. Dùng `async/await` thay cho `.then()` để code gọn hơn.