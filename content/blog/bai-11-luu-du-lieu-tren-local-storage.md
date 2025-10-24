---
title: "Lưu dữ liệu trên trình duyệt với Local Storage và Session Storage"
date: 2025-10-23
tags: ["JavaScript", "Web Storage", "Local Storage", "Session Storage", "Frontend"]
summary: "Tìm hiểu cách lưu trữ dữ liệu trực tiếp trên trình duyệt bằng Local Storage và Session Storage, giúp web hoạt động nhanh hơn và ghi nhớ thông tin người dùng."
cover:
  image: "/images/javascript/local-storage.jpg"
  alt: "Minh họa lưu dữ liệu trên trình duyệt bằng Local Storage và Session Storage"
---

## 1. Giới thiệu

Khi bạn truy cập một trang web, liệu bạn có bao giờ tự hỏi vì sao trình duyệt có thể “nhớ” được tên đăng nhập, giỏ hàng, hoặc chế độ tối/sáng?

Câu trả lời nằm ở **Web Storage API** — trong đó có **Local Storage** và **Session Storage**, cho phép JavaScript **lưu dữ liệu tạm thời hoặc lâu dài ngay trong trình duyệt** mà không cần server.

---

## 2. Sự khác nhau giữa Local Storage và Session Storage

| Tính năng             | Local Storage                            | Session Storage                        |
| --------------------- | ---------------------------------------- | -------------------------------------- |
| **Thời gian lưu**     | Vĩnh viễn (cho đến khi bị xóa)           | Chỉ tồn tại trong phiên làm việc (tab) |
| **Kích thước tối đa** | Khoảng 5MB                               | Khoảng 5MB                             |
| **Truy cập**          | Dữ liệu vẫn còn sau khi đóng trình duyệt | Mất khi đóng tab hoặc cửa sổ           |
| **Phạm vi**           | Mọi tab cùng domain                      | Riêng từng tab                         |

---

## 3. Cách sử dụng Local Storage

### 🧠 Cú pháp cơ bản

```javascript
// Lưu dữ liệu
localStorage.setItem("username", "Ngọc Trúc");

// Lấy dữ liệu
const user = localStorage.getItem("username");
console.log(user); // "Ngọc Trúc"

// Xóa một mục
localStorage.removeItem("username");

// Xóa toàn bộ
localStorage.clear();
```
Lưu ý: Dữ liệu được lưu ở dạng chuỗi (string). Nếu bạn muốn lưu đối tượng hoặc mảng, hãy chuyển sang JSON.
```javascript
const settings = { theme: "dark", fontSize: 18 };
localStorage.setItem("settings", JSON.stringify(settings));

const savedSettings = JSON.parse(localStorage.getItem("settings"));
console.log(savedSettings.theme); // dark
```
---
## 4. Cách sử dụng Session Storage

Cú pháp tương tự Local Storage, chỉ khác là dữ liệu mất khi đóng tab:

```javascript
sessionStorage.setItem("cart", JSON.stringify(["Laptop", "Chuột"]));
const cart = JSON.parse(sessionStorage.getItem("cart"));
console.log(cart); // ["Laptop", "Chuột"]
```

## 5. Ví dụ thực tế: Ghi nhớ chế độ giao diện (Dark/Light Mode)

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <title>Dark Mode Demo</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
      transition: all 0.3s ease;
      text-align: center;
      padding: 50px;
    }
    button {
      background: #3498db;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 16px;
    }
    button:hover {
      background: #2980b9;
    }
  </style>
</head>
<body>
  <h1>🌗 Chế độ giao diện</h1>
  <button id="toggleBtn">Chuyển chế độ</button>
  
  <script src="script.js"></script>
</body>
</html>
```

### JavaScript (script.js)

```javascript
const body = document.body;
const btn = document.getElementById("toggleBtn");

function applyTheme(theme) {
  if (theme === "dark") {
    document.documentElement.style.setProperty("--bg", "#121212");
    document.documentElement.style.setProperty("--text", "#ffffff");
  } else {
    document.documentElement.style.setProperty("--bg", "#ffffff");
    document.documentElement.style.setProperty("--text", "#000000");
  }
}

// Lấy chế độ đã lưu
let currentTheme = localStorage.getItem("theme") || "light";
applyTheme(currentTheme);

btn.addEventListener("click", () => {
  currentTheme = currentTheme === "light" ? "dark" : "light";
  localStorage.setItem("theme", currentTheme);
  applyTheme(currentTheme);
});
```

Khi bạn chọn chế độ tối, trình duyệt sẽ ghi nhớ lựa chọn này và tự động áp dụng lại ngay cả khi bạn đóng và mở lại trang.

## 6. Tổng kết

| API             | Mục đích                                              | Thời gian lưu  |
| --------------- | ----------------------------------------------------- | -------------- |
| Local Storage   | Lưu thông tin lâu dài (cài đặt, token, theme)         | Vĩnh viễn      |
| Session Storage | Lưu thông tin tạm thời (dữ liệu form, trạng thái tab) | Khi tab còn mở |

## 7. Bài tập thực hành

1. Tạo ứng dụng "Ghi chú cá nhân" lưu ghi chú bằng Local Storage.
2. Tạo form đăng nhập demo, lưu trạng thái đăng nhập tạm thời bằng Session Storage.
3. Kết hợp Fetch API + Local Storage để cache dữ liệu API, giúp load nhanh hơn khi người dùng mở lại trang.