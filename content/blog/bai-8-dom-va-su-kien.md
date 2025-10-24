---
title: "DOM và Event trong JavaScript"
date: 2025-10-22
tags: ["JavaScript", "DOM", "Event", "Frontend"]
category: "javascript"
lesson_number: "08"
summary: "Hiểu DOM (Document Object Model), cách JavaScript truy cập và thay đổi phần tử HTML, cũng như lắng nghe sự kiện (click, input) để tạo trang web tương tác."
cover:
  image: "/images/js/dom-events.jpg"
  alt: "Minh họa DOM và sự kiện trong JavaScript"
---

# Bài 8. DOM và Sự kiện – Làm web tương tác

## Mục tiêu

- Hiểu **DOM (Document Object Model)** là gì và vì sao nó quan trọng.
- Biết cách **truy cập và thay đổi phần tử HTML** bằng JavaScript.
- Làm quen với **xử lý sự kiện (Event Handling)** như `click`, `input`, `change`.
- Tự tay làm **ví dụ tương tác** như đổi màu nền hoặc hiển thị thời gian thực.

---

## 1. DOM là gì?

Khi trình duyệt tải một trang HTML, nó sẽ biến toàn bộ cấu trúc của trang đó thành **cây DOM (Document Object Model)**.

DOM không chỉ là "nội dung" mà còn là "mô hình dữ liệu" để JavaScript **truy cập, sửa đổi, hoặc thêm mới phần tử**.

### Ví dụ HTML:

```html
<body>
  <h1 id="title">Xin chào!</h1>
  <p class="desc">Đây là đoạn mô tả.</p>
</body>
```

### Cấu trúc DOM sẽ trông như thế này:

```mermaid
graph TD
    A[Document] --> B[html]
    B --> C[head]
    B --> D[body]
    D --> E[h1 id="title"]
    D --> F[p class="desc"]
```

JavaScript có thể "đi vào" cây DOM để thay đổi bất kỳ phần tử nào.

## 2. Truy cập phần tử HTML

### Cách 1: getElementById()

Truy cập phần tử thông qua id.

```javascript
const title = document.getElementById("title");
title.textContent = "Xin chào, JavaScript!";
```

### Cách 2: querySelector()

Truy cập phần tử bằng CSS selector (rất linh hoạt).

```javascript
const desc = document.querySelector(".desc");
desc.style.color = "blue";
```

### Cách 3: querySelectorAll()

Trả về tất cả các phần tử khớp selector (NodeList).

```javascript
const items = document.querySelectorAll("p");
items.forEach(item => item.style.fontWeight = "bold");
```

## 3. Thay đổi nội dung và thuộc tính

JavaScript cho phép bạn thay đổi nội dung, màu sắc, hoặc thêm class cho phần tử.

```javascript
const box = document.getElementById("title");
box.textContent = "Chào mừng đến với DOM!";
box.style.backgroundColor = "lightcoral";
box.style.padding = "10px";
```

**Kết quả:** phần tử `<h1>` đổi nội dung và có nền màu đỏ nhạt.

## 4. Sự kiện (Event) trong JavaScript

Sự kiện là bất kỳ hành động nào người dùng thực hiện: nhấp chuột, nhập dữ liệu, rê chuột, cuộn trang...

### Một số sự kiện phổ biến:

| Sự kiện     | Mô tả                        |
| ----------- | ---------------------------- |
| `click`     | Khi người dùng nhấp chuột    |
| `mouseover` | Khi con trỏ rê qua phần tử   |
| `input`     | Khi người dùng nhập dữ liệu  |
| `change`    | Khi giá trị đầu vào thay đổi |
| `keydown`   | Khi người dùng nhấn phím     |

## 5. Ví dụ: Nút đổi màu nền

### HTML:

```html
<h2>Thay đổi màu nền</h2>
<button id="btn">Đổi màu</button>
```

### JavaScript:

```javascript
const btn = document.getElementById("btn");
btn.addEventListener("click", function() {
  document.body.style.backgroundColor = 
    document.body.style.backgroundColor === "lightblue" ? "white" : "lightblue";
});
```

Mỗi lần bạn bấm nút, trang sẽ đổi màu nền giữa trắng và xanh nhạt.

## 6. Ví dụ nâng cao: Hiển thị thời gian thực

### HTML:

```html
<p>Giờ hiện tại: <span id="clock"></span></p>
```

### JavaScript:

```javascript
function updateClock() {
  const now = new Date();
  const time = now.toLocaleTimeString();
  document.getElementById("clock").textContent = time;
}

setInterval(updateClock, 1000);
```

**Kết quả:** Đồng hồ chạy từng giây ngay trên trang web

## 7. Cách gắn sự kiện trong HTML (không khuyến khích)

Bạn có thể gắn sự kiện trực tiếp trong HTML:

```html
<button onclick="alert('Xin chào!')">Bấm vào đây</button>
```

Tuy nhiên, cách này khó quản lý khi dự án lớn.  
Cách dùng `addEventListener()` (ở trên) là chuẩn hơn.

## 8. Tóm tắt kiến thức

| Khái niệm                      | Mô tả                                      |
| ------------------------------ | ------------------------------------------ |
| DOM                            | Mô hình cây đại diện cho tài liệu HTML     |
| getElementById / querySelector | Dùng để truy cập phần tử                   |
| addEventListener               | Dùng để lắng nghe sự kiện                  |
| Event                          | Hành động của người dùng (click, input, …) |

## Kết luận

DOM và sự kiện là trái tim của lập trình web bằng JavaScript, nhờ có nó:
- Giao diện thay đổi linh hoạt mà không cần tải lại trang.
- Trang web phản ứng với từng hành động người dùng.

Trong bài tiếp theo, bạn sẽ học cách để JavaScript giao tiếp với server — tải dữ liệu, hiển thị bài viết, hoặc gửi yêu cầu API bằng Fetch API và JSON.