# OOP và JAVA
- 4 tính chất OOP: 
    - Encapsulation (đóng gói): private, public,...
    - Inheritance (kế thừa): overriding (ghi đè phương thức)
    - Polymorphism (đa hình): thể hiện ở overloading (nạp chồng phương thức)
    - Abstraction (trừu tượng hóa): thể hiện qua abstract classes và interfaces
-  Collections Framework: List (ArrayList, LinkedList), Set (HashSet, LinkedHashSet, TreeSet), Map (HashMap, TreeMap, LinkedHashMap)
-  Lambda Expressions: List<String> list = Arrays.asList("Java", "Python", "C++"); list.forEach(s -> System.out.println(s));
- Stream API
- Optional
- Concurrency and Multithreading
- Design Patterns
- Reflection
- Annotations
- Java Memory Management
- JVM Internals
- Java Module System

-------

1. Collections api
- List là tập hợp có thứ tự và cho phép các phần tử trùng lặp: 
    + ArrayList: tập hợp có thể thay đổi kích thước động
    + LinkedList: tập hợp sử dụng cấu trúc danh sách liên kết, cho phép thêm/xóa phần tử  ở đầu hoặc cuối hiệu quả.
- Set là tập hợp không cho phép các phần tử trùng lặp:
    + HashSet: tập hợp không có thứ tự, không cho phép phần tử trùng  lặp.
    + LinkedHashSet: tập hợp không cho phép phần tử trùng lặp nhưng duy trì thứ tự các phần tử theo thứ tự thêm vào.
    + TreeSet: tập hợp có thứ tự, các phần tử được tự động sắp xếp theo thứ tự tự nhiên hoặc theo một bộ so sánh.
- Queue là một cấu trúc dữ liệu kiểu hàng đợi (FIFO), các phần tử được thêm vào cuối và lấy ra từ đầu: 
    + PriorityQueue: một hàng đợi cho phép các phần tử có thứ tự theo mức độ ưu tiên
    + LinkedList: có thể hoạt động như 1 Queue
- Map là một tập hợp của các cặp khóa – giá trị, trong đó mỗi khóa là duy nhất:
    + HashMap: lưu trữ các cặp khóa – giá trị không có thứ tự.
    + TreeMap: Lưu trữ các cặp khóa – giá trị với thứ tự sắp xếp
    + LinkedHashMap: lưu trữ các cặp khóa – giá trị theo thứ tự thêm vào.
Các phương thức phổ biến:
        - add(E e): Thêm phần tử vào collection.
        - remove(Object o): Loại bỏ phần tử từ collection.
        - contains(Object o): Kiểm tra xem collection có chứa phần tử hay không.
        - size(): Lấy kích thước của collection.
        - clear(): Xóa tất cả các phần tử trong collection 
2. Stream Api
- không phải là một cấu trúc dữ liệu. Nó cho phép thực hiện các phép toán trên các phần tử của 1 collection mà không làm thay đổi nguồn dữ liệu ban đầu. Các phép toán trên stream có thể được thực hiện một cách tuần tự hoặc song song. Các phép toán:
- Intermediate operations (phép toán trung gian): tạo một stream mới từ stream hiện tại. Các phép toán này không thực thi ngay mà chỉ đánh dấu các hành động cần làm khi có một terminal operation:
    - filter(): Loại bỏ các phần tử không thỏa mãn điều kiện.
    - map(): Chuyển đổi mỗi phần tử trong stream thành một giá trị khác (thường dùng cho việc biến đổi kiểu dữ liệu).
    - sorted(): Sắp xếp các phần tử trong stream.
    - distinct(): Loại bỏ các phần tử trùng lặp trong stream.
    - peek(): Giúp xem qua các phần tử trong stream mà không làm thay đổi chúng. Thường dùng cho mục đích debug. 
- Terminal Operations: các phép toán này thực sự thực thi và trả về kết quả cuối cùng.
    - collect(): Thu thập các phần tử trong stream vào một tập hợp (ví dụ: List, Set).
    - forEach(): Duyệt qua các phần tử trong stream và thực thi một hành động.
    - reduce(): Gom nhóm các phần tử thành một giá trị duy nhất (ví dụ: tính tổng, tích).
    - count(): Đếm số lượng phần tử trong stream.
    - anyMatch(), allMatch(), noneMatch(): Kiểm tra điều kiện cho tất cả hoặc một phần tử trong stream.
    - findFirst(), findAny(): Lấy phần tử đầu tiên hoặc bất kỳ phần tử nào trong stream. 
    ```java
    List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry", "Date");
    Stream<String> fruitStream = fruits.stream();
    fruitStream
        .filter(fruit -> fruit.length() > 5)  // Lọc các phần tử có độ dài lớn hơn 5
        .map(String::toUpperCase)             // Chuyển đổi mỗi phần tử thành chữ hoa
        .sorted()                             // Sắp xếp các phần tử
        .forEach(System.out::println);        // Duyệt và in ra
    ```
3. Tham chiếu, tham trị

| Tiêu chí so sánh         | Tham trị (Pass-by-value)                                       | Tham chiếu (Pass-by-reference)                              |
|--------------------------|---------------------------------------------------------------|-------------------------------------------------------------|
| **Khi được sử dụng**     | Truyền kiểu dữ liệu nguyên thủy (primitive types) vào phương thức | Truyền đối tượng vào phương thức                            |
|                          | Truyền giá trị của biến vào                                  | Truyền tham chiếu (địa chỉ bộ nhớ) của đối tượng            |
| **Phạm vi ảnh hưởng**    | Bất kì thay đổi nào trong phương thức sẽ không ảnh hưởng đến giá trị của biến gốc. | Thay đổi thuộc tính của đối tượng trong phương thức sẽ ảnh hưởng đến đối tượng gốc. |


 
