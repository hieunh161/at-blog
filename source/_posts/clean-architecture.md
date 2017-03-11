---
title: The Clean Architecture
date: 2016-11-01 22:08:29
category: memo
tags: Architecture

---

## Kiến trúc Clean

Liên quan tới kiến trúc hệ thống từ trước tới nay có rất nhiều người nghiên cứu và đưa ra rất nhiều kiến trúc khác nhau. Mỗi kiến trúc đều có những nét đặc trưng riêng và có thể áp dụng cho những bài toán cụ thể rất hiệu quả, nhưng khi nhìn về mục đích thì chúng đều có một điểm rất giống nhau đó là những yếu tố khi phân tách hệ thống. Tất cả những kiến trúc đều cố gắng phân chia phần mềm thành các lớp khác nhau. Thông thường ở bất kỳ cấu trúc nào cũng có một lớp cho GUI và một lớp cho business logic. Tổng hợp chung chúng ta có thể thấy các mối quan tâm giải quyết chủ đạo của các kiến trúc đều dưa tới các mục đích sau:
<!-- more -->
1. Khả năng độc lập với các framework. Thông thường một kiến trúc hệ thống không phụ thuộc vào bất kỳ một thư viện phần mềm có sẵn nào. Do đó nó cho phép chúng ta tùy ý sử dụng các thư viện, framework hay tool khác thay vì gò ép hệ thống phần mềm bởi các ràng buộc giới hạn.
2. Khả năng độc lập testing. Các quy tắc, nghiệp vụ business có thể test mà không cần UI, Database, Web Server hay bất kỳ một yếu tố bên ngoài nào.
3. Khả năng độc lập của UI. GUI có thể thay đổi dễ dàng mà không ảnh hưởng tới các thành phần khác của hệ thống. Một màn hình WebUI có thể dễ dàng được thay đổi bởi một màn hình console UI mà không phải thay đổi business logic có sẵn.
4. Khả năng độc lập của Database. Chúng ta có thể thay đổi database mà không ảnh hưởng tới business logic có sẵn. Ví dụ chúng ta có thể thay NoSQL cho SQL, thay file xml bằng file json ...
5. Khả năng độc lập các thành phần bên ngoài. Business logic độc lập hoàn toàn với các yếu tố bên ngoài.

Hình vẽ phía dưới tổng hợp lại thành một kiến trúc hệ thống miêu tả đầy đủ các đặc tả phía trên.
![Clean Architecture](https://c2.staticflickr.com/6/5501/30673953886_ef87d28da1_b.jpg)

## Nguyên tắc phụ thuộc

Các vòng tròn đồng tâm ở phía trên miêu tả các vùng khác nhau của phần mềm. Theo một nguyên tắc chung, các vòng tròn càng ở xa thì phần mềm càng ở level cao. Các vòng tròn bên ngoài là các cơ chế hoạt động của phần mềm, các vòng tròn bên trong là các nguyên tắc, quy tắc vận hành của phần mềm. Từ đây chúng ta có thể nhìn thấy sự phụ thuộc của source code luôn phải hướng vào phía trong. Các vòng tròn phía trong không có lý do nào cần phải biết hoạt động của các vòng bên ngoài. Trong source code, các khai báo của các source nằm ở vòng tròn bên ngoài không được phép xuất hiện trong source ở các vòng tròn bên trong. Nó có thể là biến, class, hàm hoặc bất kỳ một thực thể phần mềm nào. Điều này còn đúng với cả các format data khi các format data ở các vòng bên ngoài không nên được sử dụng ở trong các vòng tròn bên trong.

![Dependency Rule](https://c2.staticflickr.com/6/5801/30596140962_48d55e6480_b.jpg)

## Các thực thể

Các thực thể chính là các đóng gói của các quy tắc business. Một thực thể có thể là một đối tượng với các phương thức hoặc nó có thể đơn thuần là một tập hợp các cấu trúc dữ liệu và các hàm. Đây là các đối tượng ít bị tác động thay đổi nhất khi có sự thay đổi bên ngoài tác động rồi. Ví dụ như các thực thể không thể bị thay đổi khi có sự thay đổi liên quan tới chuyển page, bảo mật ...

## Các use case

Phần mềm ở lớp này chứa các quy tắc business cụ thể của ứng dụng. Nó đóng gói thực thi của các trường hợp sử dụng hệ thống, nó mô tả các dòng dữ liệu giữa các thực thể, và gắn kết các thực thể với các quy tắc business để đạt được mục đích sử dụng đề ra.

Các thay đổi ở lớp ngoài như UI hay database theo nguyên tắc phụ thuộc sẽ không ảnh hưởng tới lớp này. Tuy nhiên các thay đổi liên quan tới vận hành ứng dụng sẽ ảnh hưởng tới lớp này. 

## Các bộ chuyển đổi giao diện

Phần mềm ở lớp này sẽ chịu trách nhiệm chuyển đổi dữ liệu trở thành dễ dàng sử dụng cho các lớp phần mềm bên trong là use case và entity. Ví dụ nó sẽ chuyển đổi dữ liệu lấy ra từ database theo format mà usecase có thể sử dụng được. View, Presenter, Controller trong các mô hình MVC, MVP đều thuộc lớp này. Model trong các mô hình này chính là các cấu trúc dữ liệu truyền từ controller tới các usecase, sau đó back từ usecase lên view và presenter.

Tương tự, lớp này cũng có trách nhiệm chuyển đổi ngược từ dữ liệu ở định dạng của usecase thành định dạng mà các yếu tố bên ngoài ví dụ như database có thể lưu trữ và sử dụng.

## Framework, Tool, Driver

Lớp ngoài cùng trong kiến trúc clean chính là các framework và tool như database, files. Thông thường chúng ta sẽ ít khi cần phải code các công cụ này, thay vào đó chúng ta chỉ cần làm sao để có thể kết nối và gắn kết các thành phần này vào hệ thống của chúng ta.

## Kết luận

Mục đích chung của các kiến trúc hệ thống là phân tách các thành phần của hệ thống, từ đó có thể thực hiện nó một cách riêng biệt, ít ảnh hưởng tới các thành phần khác. Và khi có bất cứ thay đổi nào chúng ta có thể đánh giá được ảnh hưởng của nó tới thành phần khác. Clean architecture cho chúng ta một bức tranh rõ nét về các lớp trong phần mềm và quan trọng hơn hết là quy tắc phụ thuộc giữa các thành phần này.

