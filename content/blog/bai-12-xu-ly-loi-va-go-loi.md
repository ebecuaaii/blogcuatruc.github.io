---
title: "Xử lý lỗi và gỡ lỗi trong JavaScript – Làm chủ Debugging & Error Handling"
date: 2025-10-23
tags: ["JavaScript", "Debugging", "Error Handling", "Console", "Try Catch"]
summary: "Học cách phát hiện, theo dõi và xử lý lỗi trong JavaScript bằng console, try...catch và công cụ DevTools – kỹ năng quan trọng giúp bạn viết code ổn định và dễ bảo trì."
cover:
  image: "/images/javascript/debugging.jpg"
  alt: "Minh họa Debugging trong JavaScript"
---

##  1. Giới thiệu

Không lập trình viên nào tránh khỏi lỗi. Dù bạn là người mới học hay đã có kinh nghiệm, việc biết **gỡ lỗi (debugging)** và **xử lý lỗi (error handling)** là kỹ năng bắt buộc để viết phần mềm ổn định và đáng tin cậy.

Trong JavaScript, ta có rất nhiều cách để **phát hiện**, **theo dõi** và **khắc phục lỗi**, bao gồm:
- Sử dụng **Console API** để kiểm tra biến, log dữ liệu.
- Dùng **try...catch...finally** để xử lý ngoại lệ.
- Hiểu các **loại lỗi phổ biến** và cách tránh chúng.
- Dùng **DevTools của trình duyệt** để debug từng bước.

---

##  2. Dò lỗi bằng Console

`console` là công cụ thân quen của mọi lập trình viên web.

### Các hàm thường dùng

```javascript
console.log("Xin chào, đây là log!");
console.warn("Cảnh báo: dữ liệu đầu vào trống!");
console.error("Lỗi nghiêm trọng!");
console.table([{ name: "An", age: 20 }, { name: "Bình", age: 22 }]);
```
Kết quả sẽ hiển thị trực tiếp trong tab Console của DevTools (F12 trên Chrome/Edge).

### Mẹo nhỏ

- Dùng `console.log()` để kiểm tra biến tại từng bước.
- Dùng `console.time()` và `console.timeEnd()` để đo thời gian chạy đoạn code.
- Dùng `console.group()` để nhóm log gọn gàng.

## 3. Các loại lỗi phổ biến trong JavaScript

| Loại lỗi       | Nguyên nhân               | Ví dụ                                              |
| -------------- | ------------------------- | -------------------------------------------------- |
| SyntaxError    | Sai cú pháp               | `if (x > 5 {`                                      |
| ReferenceError | Biến chưa được định nghĩa | `console.log(userName);` // userName chưa khai báo |
| TypeError      | Gọi sai kiểu dữ liệu      | `"hello".push("!")`                                |
| RangeError     | Giá trị nằm ngoài phạm vi | `(12345).toFixed(200)`                             |
| NetworkError   | Lỗi khi gọi API           | `fetch("https://wrong-url.com")`                   |

## 4. Xử lý lỗi bằng try...catch...finally

### Cú pháp:

```javascript
try {
  // đoạn code có thể gây lỗi
  let result = JSON.parse('{"name":"Ngọc"}');
  console.log(result.name);
} catch (error) {
  console.error("Có lỗi xảy ra:", error.message);
} finally {
  console.log("Luôn chạy dù có lỗi hay không");
}
```

### Giải thích:

- **try**: chứa đoạn code có khả năng gây lỗi.
- **catch**: chạy khi xảy ra lỗi. Có thể truy cập chi tiết lỗi qua biến `error`.
- **finally**: luôn chạy, thường dùng để dọn dẹp tài nguyên.

## 5. Tự tạo lỗi với throw

Bạn có thể chủ động ném lỗi khi dữ liệu không hợp lệ:

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error("Không thể chia cho 0!");
  }
  return a / b;
}

try {
  console.log(divide(10, 0));
} catch (e) {
  console.error("Lỗi:", e.message);
}
```

## 6. Debug từng bước bằng DevTools

1. Mở DevTools (F12) → tab Sources → đặt breakpoint ở dòng bạn muốn dừng.
2. Sau đó:
   - Nhấn **F10** để chạy từng dòng.
   - Nhấn **F8** để tiếp tục chương trình.
   - Quan sát giá trị biến trong **Scope**.

Cách này cực hữu ích khi chương trình dài hoặc lỗi không rõ nguyên nhân.

## 7. Ví dụ thực tế: Debug lỗi khi gọi API

```javascript
async function fetchPosts() {
  try {
    console.log("Đang gọi API...");
    const res = await fetch("https://jsonplaceholder.typicode.com/postsss"); // lỗi URL
    if (!res.ok) throw new Error("Lỗi kết nối API!");
    const data = await res.json();
    console.table(data.slice(0, 3));
  } catch (err) {
    console.error("Không thể tải dữ liệu:", err.message);
  } finally {
    console.log("Kết thúc quá trình fetch.");
  }
}

fetchPosts();
```

### Giải thích:

- Nếu đường dẫn API sai, `fetch()` sẽ ném lỗi.
- `catch` sẽ bắt lỗi và log ra console.
- `finally` vẫn chạy để thông báo hoàn tất.

## 8. Thực hành nhanh

1. Viết chương trình nhập số chia (a, b), xử lý lỗi chia cho 0.
2. Thử ném lỗi khi JSON không hợp lệ (`JSON.parse('{name:"An"}')`).
3. Gọi API sai URL và xử lý lỗi mạng.
4. Đặt breakpoint và debug từng dòng trong DevTools.

## 9. Tổng kết

| Kỹ thuật                   | Mục đích                            |
| -------------------------- | ----------------------------------- |
| console.log / warn / error | In thông tin ra console để theo dõi |
| try...catch...finally      | Xử lý lỗi runtime                   |
| throw new Error()          | Chủ động tạo lỗi                    |
| DevTools (breakpoint)      | Gỡ lỗi trực quan                    |
| Error Object               | Cung cấp thông tin chi tiết về lỗi  |

## Kết luận

Lỗi là một phần tự nhiên của lập trình — nhưng biết cách debug và xử lý lỗi thông minh sẽ giúp bạn tiết kiệm hàng giờ làm việc và tạo ra phần mềm đáng tin cậy.

Một lập trình viên giỏi không phải là người không bao giờ gặp lỗi, mà là người biết cách hiểu và sửa lỗi thật nhanh!

## Tài liệu tham khảo

- [MDN Web Docs – try...catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)
- [Chrome DevTools Guide](https://developer.chrome.com/docs/devtools/)
- [JavaScript Error Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Errors)
