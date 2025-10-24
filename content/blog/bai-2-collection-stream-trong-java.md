---
title: "Hiểu Collection và Stream trong Java chỉ trong 15 phút"
date: 2025-10-21
tags: ["Java", "Collection", "Stream", "API"]
summary: "Giới thiệu các collection phổ biến (List, Set, Map) và cách dùng Stream API để xử lý dữ liệu theo phong cách hàm (filter, map, collect, group)."
cover:
  image: "/images/java/collections-streams.jpg"
  alt: "Hình minh họa Collection và Stream trong Java"
---

## Mở đầu

Khi lập trình bằng Java, bạn sẽ rất thường xuyên thao tác với **tập hợp dữ liệu** — danh sách người, tập khóa, bảng mapping. Java cung cấp **Collection Framework** để quản lý các cấu trúc này, và **Stream API** (từ Java 8) để xử lý dữ liệu theo phong cách hàm, ngắn gọn và dễ đọc.

Trong bài này bạn sẽ học:
- Các collection phổ biến: `List`, `Set`, `Map`
- Các thao tác cơ bản: thêm, xóa, tìm, duyệt
- Stream API: `filter`, `map`, `sorted`, `collect`, `groupingBy`, `flatMap`
- Ví dụ thực tế và cách chạy

---

## Phần 1 — Các Collection cơ bản

### 1. `List` (ví dụ: `ArrayList`)
`List` là danh sách có thứ tự, cho phép trùng phần tử.
```java
import java.util.*;

List<String> names = new ArrayList<>();
names.add("An");
names.add("Binh");
names.add("Chi");
names.add("An"); // cho phép trùng
System.out.println(names); // [An, Binh, Chi, An]
```

## 2. Set (ví dụ: HashSet)

Set là tập hợp không cho phép trùng, không bảo đảm thứ tự (nếu cần thứ tự dùng LinkedHashSet).

```java
Set<String> unique = new HashSet<>();
unique.add("An");
unique.add("Binh");
unique.add("An"); // sẽ bị bỏ
System.out.println(unique); // ví dụ: [Binh, An]
```

## 3. Map (ví dụ: HashMap)

Map lưu trữ cặp key-value, dùng để tra cứu theo khóa.

```java
Map<String, Integer> age = new HashMap<>();
age.put("An", 21);
age.put("Binh", 22);
System.out.println(age.get("An")); // 21
```

##  Phần 2 — Duyệt và thao tác truyền thống (imperative)

### Duyệt một List bằng vòng lặp for:

```java
for (String n : names) {
    System.out.println(n);
}
```

### Sử dụng iterator:

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    String n = it.next();
    if (n.equals("An")) it.remove(); // xóa an toàn khi duyệt
}
```

## Phần 3 — Stream API (phong cách hàm)

Stream cho phép xử lý tập dữ liệu theo pipeline: **source → intermediate ops → terminal op**

Ví dụ: lọc, chuyển đổi, sắp xếp, thu thập kết quả.

### Ví dụ cơ bản

```java
import java.util.*;
import java.util.stream.*;

List<String> names = List.of("An", "Binh", "Chi", "An", "Dung");

// Lọc tên không trùng, chuyển thành chữ hoa, và thu về List
List<String> result = names.stream()
    .distinct()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

System.out.println(result); // [AN, BINH, CHI, DUNG]
```

### Các thao tác thường dùng

- `filter(predicate)` — lọc phần tử
- `map(func)` — biến đổi từng phần tử
- `flatMap(...)` — nối nhiều stream con thành 1
- `distinct()` — loại trùng
- `sorted()` / `sorted(comparator)` — sắp xếp
- `limit(n)` / `skip(n)` — lấy hoặc bỏ
- `collect(Collectors.toList())` / `toSet()` / `toMap()` — thu thập kết quả

### Ví dụ thực tế: danh sách học sinh, lọc và nhóm

### Giả sử có class Student:

```java
public class Student {
    private String name;
    private double gpa;
    private String major;
    // constructor, getters, toString
}
```

### Tạo dữ liệu mẫu và sử dụng Stream:

```java
List<Student> students = List.of(
    new Student("An", 3.2, "CS"),
    new Student("Binh", 2.8, "IT"),
    new Student("Chi", 3.7, "CS"),
    new Student("Dung", 3.5, "IT")
);

// Lọc các sinh viên GPA >= 3.0, sắp xếp giảm dần theo GPA
List<Student> top = students.stream()
    .filter(s -> s.getGpa() >= 3.0)
    .sorted(Comparator.comparingDouble(Student::getGpa).reversed())
    .collect(Collectors.toList());

// Nhóm theo ngành và đếm số lượng
Map<String, Long> countByMajor = students.stream()
    .collect(Collectors.groupingBy(Student::getMajor, Collectors.counting()));
```

Kết quả `countByMajor` có thể là `{CS=2, IT=2}`.

##  Một số ví dụ nâng cao

### 1) groupingBy và mapping

Lấy map: ngành → list tên sinh viên

```java
Map<String, List<String>> namesByMajor = students.stream()
    .collect(Collectors.groupingBy(
        Student::getMajor,
        Collectors.mapping(Student::getName, Collectors.toList())
    ));
```

### 2) flatMap dùng cho nested collections

Giả sử bạn có `List<List<String>>`:

```java
List<List<String>> all = List.of(
    List.of("a","b"),
    List.of("c","d")
);
List<String> flat = all.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList()); // [a,b,c,d]
```

### 3) Tổng, trung bình, thống kê

```java
DoubleSummaryStatistics stats = students.stream()
    .collect(Collectors.summarizingDouble(Student::getGpa));
System.out.println(stats.getAverage()); // trung bình GPA
```

## ⚠️ Lưu ý về hiệu năng và song song

- `stream()` là nối tiếp (sequential).
- `parallelStream()` cho phép xử lý song song — tốt cho tập lớn nhưng cẩn thận với side-effects (tránh modify shared state).
- Streams tốt cho readability, nhưng không phải lúc nào cũng nhanh hơn vòng lặp truyền thống (tùy bản chất công việc và kích thước dữ liệu).

### Ví dụ dùng parallel:

```java
long count = bigList.parallelStream().filter(x -> expensiveCheck(x)).count();
```

##  Thực hành — ví dụ đầy đủ (chạy được)

### Tạo file ExampleStream.java:

```java
import java.util.*;
import java.util.stream.*;

public class ExampleStream {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("An","Binh","Chi","An","Dung","Huy");
        
        // 1. distinct + map + sorted
        List<String> processed = names.stream()
            .distinct()
            .map(String::toUpperCase)
            .sorted()
            .collect(Collectors.toList());
        System.out.println("Processed: " + processed);
        
        // 2. filter and count
        long aCount = names.stream().filter(n -> n.startsWith("A")).count();
        System.out.println("Start with A: " + aCount);
        
        // 3. grouping example
        Map<Integer, List<String>> byLength = names.stream()
            .collect(Collectors.groupingBy(String::length));
        System.out.println("Grouped by length: " + byLength);
    }
}
```

### Biên dịch và chạy:

```bash
javac ExampleStream.java
java ExampleStream
```

### Ví dụ đầu ra có thể:

```
Processed: [AN, BINH, CHI, DUNG, HUY]
Start with A: 2
Grouped by length: {2=[An], 4=[Binh, Dung, Huy], 3=[Chi]}
```

##  Mẹo & Best Practices

- Dùng Stream để viết ngắn gọn và dễ đọc; tránh side-effects trong map/filter.
- Dùng `Collectors.toMap()` cẩn thận khi keys có thể trùng — cần cung cấp merge function.
- Khi cần performance tuning, đo đạc (benchmark) trước khi chuyển sang `parallelStream`.
- Sử dụng `Optional` để tránh `NullPointerException` khi lấy kết quả từ stream.

## Bài tập thực hành (3 bài)

1. Viết chương trình đếm số từ độc nhất trong 1 danh sách `List<String>` (không phân biệt hoa thường).
2. Từ danh sách `Student` (có major và gpa), lấy top 3 sinh viên có GPA cao nhất trong ngành CS.
3. Tạo `Map<Character, List<String>>` nhóm tên theo chữ cái đầu (A → [An, ...]).

## Tham khảo & đọc tiếp

- Stream API (Java 8) — `java.util.stream`
- Các Collector hữu ích: `groupingBy`, `partitioningBy`, `mapping`, `summarizingDouble`

##  Kết luận

Collection + Stream là công cụ không thể thiếu trong Java hiện đại. Nắm vững chúng giúp bạn xử lý dữ liệu nhanh chóng, code ngắn gọn và dễ bảo trì. 

Ở bài tiếp theo, chúng ta sẽ bàn về xử lý ngoại lệ (Exception) và logging trong Java — rất cần thiết khi ứng dụng bạn lớn dần.
