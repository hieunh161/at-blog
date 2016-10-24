---
title: Những điểm mới trong ES6 (Phần 1)
date: 2016-10-22 12:29:38
tags: 
     - javascript 
     - es6
category: memo

---

## Transpilers

Nếu bạn là tín đồ của javascript chắc hẳn bạn phải biết tới đặc tả tiêu chuẩn của javascript là ECMAScript. Hiện nay các phiên bản javascript chúng ta sử dụng hầu hết được viết dựa trên ECMAScript version 5. Version 6 của ECMAScript đã ra đời với rất nhiều tính năng đáng chú ý, tuy nhiên chưa có nhiều trình duyệt hỗ trợ hoàn toàn phiên bản mới này. (Edge của Microsoft là một trong số ít các trình duyệt đi đầu hỗ trợ ES6). Để giải quyết vấn đề này cộng đồng đã cho ra đời một phương pháp giải quyết đó là transpiler.
<!-- more -->
Transpiler = Source to source compiler là công cụ cho phép sử dụng ES6 source biên dịch sang ES5 và chạy nó trên các trình duyệt. Hiện tại có 2 project liên quan tới transpiler đáng chú ý đó là: 
  * [Traceur](https://github.com/google/traceur-compiler) Project của google
  * [Babeljs](https://babeljs.io/) Một project được viết bởi Sebastian McKenzie (17 tuổi tại thời điểm viết babel), với rất nhiều sự đóng góp từ cộng đồng.

Do đó việc đầu tiên nếu chúng ta muốn bắt đầu một dự án dùng ES6 mà chạy trên các trình duyệt cũ hơn thì chúng ta cần quan tâm tới các công cụ transpiler này.

## Let

Biến var trong javascript khá rắc rối bởi vì javascript có một khái niệm gọi là hoisting. Nghĩa là biến thực tế sẽ được khai báo ở đàu hàm ngay cả khi chúng ta khai báo nó ở phía sau.
Ví dụ chúng ta khái báo biến name trong if block:

```javascript
function getPonyFullName(pony) {
  if (pony.isChampion) {
    var name = 'Champion ' + pony.name;
    return name;
  }
  return pony.name;
}
```

nó sẽ không khác gì đoạn code dưới:

```javascript
function getPonyFullName(pony) {
  var name;
  if (pony.isChampion) {
    name = 'Champion ' + pony.name;
    return name;
  }
  // name có thể được truy cập ở đây
  return pony.name;
}
```

ES6 cung cấp một biến mà cho phép chúng ta khai báo đúng theo cách mà chúng ta muốn đó là <strong>let</strong>

```javascript
function getPonyFullName(pony) {
  if (pony.isChampion) {
    let name = 'Champion ' + pony.name;
    return name;
  }
  // name bây giờ không thể được truy cập từ bên ngoài
  return pony.name;
}
```

Biến name bây giờ sẽ được giới hạn trong block của nó. Let được giới thiệu để thay thế var, vì thế trong hầu hết trường hợp chúng ta đều sử dụng được let. Nếu bạn không thể sử dụng let mà phải sử dụng var thì đó là một dấu hiệu cho thấy code bạn đang có vấn đề.

## Constants

ES6 giới thiệu từ khóa const cho việc khai báo hằng số. Một khi chúng ta khai báo hằng số cho một đối tượng, chúng ta không thể gán lại giá trị cho nó sau này.

```javascript
    const poniesInRace = 6;

    // later in somewhere
    poniesInRace = 7; // SyntaxError
```

Một điều vô cùng đặc biệt liên quan tới const đó là khi khai báo một object là const thì mặc dù chúng ta không thể gán nó cho đối tượng khác, nhưng chúng ta vẫn có thể thay đổi giá trị nội dung của nó.
Ví dụ đoạn code sau sẽ lỗi:

```javascript
    const PONY = {};
    PONY = {color: 'blue'}; // SyntaxError
```

Nhưng đoạn code sau sẽ chạy:

```javascript
    const PONY = {};
    PONY.color = 'blue'; // works
```

Tương tự với array và các cấu trúc khác

```javascript
    const PONIES = [];
    PONIES.push({ color: 'blue' }); // works
```

## Tạo Object

Việc tạo đối tượng trong ES6 sẽ đơn giản hơn so với ES5 khi đối tượng chúng ta muốn tạo có tên trùng với tên biến chúng ta muốn gán.
Ví dụ:

```javascript
function createPony() {
  const name = 'Rainbow Dash';
  const color = 'blue';
  return { name: name, color: color };
}
```
Đoạn code trên có thể đơn giản trở thành:

```javascript
function createPony() {
  const name = 'Rainbow Dash';
  const color = 'blue';
  return { name, color };
}
```

## Destructuring assignment

Trong ES5:

```javascript
    var httpOptions = { timeout: 2000, isCache: true };
    // later
    var httpTimeout = httpOptions.timeout;
    var httpCache = httpOptions.isCache;
```

Trong ES6 chúng ta có thể đơn giản:

```javascript
    const httpOptions = { timeout: 2000, isCache: true };
    // later
    const { timeout: httpTimeout, isCache: httpCache } = httpOptions;
```

Nếu tên biến và tên của đối tượng trùng nhau nó có thể còn trở nên đơn giản hơn nữa:

```javascript
    const httpOptions = { timeout: 2000, isCache: true };
    // later
    const { timeout, isCache } = httpOptions;
    // Chúng ta có 2 biến timeout và isCache với giá trị từ httpOptions
```

Tương tự với mảng chúng ta có

```javascript
    const timeouts = [1000, 2000, 3000];
    // later
    const [shortTimeout, mediumTimeout] = timeouts;
    // chúng ta có biến 'shortTimeout' = 1000 và 'mediumTimeout' = 2000
```

Từ trên chúng ta có thêm một tính năng rất thú vị của ES6 đó là kết quả trả về có thể là nhiều giá trị.

```javascript
function randomPonyInRace() {
  const pony = { name: 'Rainbow Dash' };
  const position = 2;
  // ...
  return { pony, position };
}
const { position, pony } = randomPonyInRace();
```

Trong trường hợp chúng ta chỉ muốn lấy một giá trị chúng ta sử dụng Destructuring để lấy:

```javascript
function randomPonyInRace() {
  const pony = { name: 'Rainbow Dash' };
  const position = 2;
  // ...
  return { pony, position };
}
const { pony } = randomPonyInRace();
```

Kết quả chúng ta sẽ chỉ lấy giá trị pony

##  Các giá trị và tham số mặc định

Một trong những đặc điểm nổi bật của javascript là cho phép người dùng gọi hàm với số lượng tham số bất kỳ
  * Nếu chúng ta truyền nhiều hơn số lượng tham số khai báo, phần vượt quá sẽ được bỏ qua. Chính sác hơn chúng ta có thể lấy phần tham số thừa thông qua biến đặc biệt là <strong>arguments</strong>
  * Nếu chúng ta truyền ít hơn tham số khai báo, phần thiếu sẽ có giá trị là <strong>undefined</strong>
Trong trường hợp thứ 2 thông thường khi muốn thêm mặc định cho biến chúng ta thường làm như sau

```javascript
function getPonies(size, page) {
  size = size || 10;
  page = page || 1;
  // ...
  server.get(size, page);
}
```

ES6 cung cấp cho chúng ta cách làm đơn giản hơn trong trường hợp này

```javascript
function getPonies(size = 10, page = 1) {
  // ...
  server.get(size, page);
}
```

>Có một chút khác biệt nhỏ giữa 2 cách. Vì 0 và "" là các giá trị hợp lệ, do đó nó sẽ không được thay thế bởi giá trị mặc định. Trong khi size = size || 10 sẽ lấy giá trị 10 khi truyền size = 0. Trong ES6 cách tính sẽ giống như dùng công thức sau size = size === undefined ? 10: size;

Giá trị mặc định có thể là hàm hoặc có thể là biến khác

```javascript
function getPonies(size = defaultSize(), page = size - 1) {
  // ...
  server.get(size, page);
}
```

Chúng ta có thể sử dụng giá trị mặc định cho các việc Destructuring biến như sau:

```javascript
    const { timeout = 1000 } = httpOptions;
    // you now have a variable named 'timeout',
    // with the value of 'httpOptions.timeout' if it exists
    // or 1000 if not
```

## Toán tử Rest ...

Vói javascript chúng ta có thể làm như sau khi truyền nhiều tham số hơn số lượng khai báo.

```javascript
function addPonies(ponies) {
  for (var i = 0; i < arguments.length; i++) {
    poniesInRace.push(arguments[i]);
  }
}
addPonies('Rainbow Dash', 'Pinkie Pie');
```
ES6 giới thiệu một cú pháp mới để định nghĩa các biến tham số trong các hàm một cách ngắn gọn hơn đó là toán tử Rest ...

```javascript
function addPonies(...ponies) {
  for (let pony of ponies) {
    poniesInRace.push(pony);
  }
} 
```

ponies giờ sẽ trở thành một mảng có thể loop được bằng cách sử dụng for ... of. (Đây cũng là một tính năng mới của ES6). Toán tử Rest được sử dụng để lấy list các tham số truyền vào hàm.
Toán tử rest cũng có thể áp dụng khi Destructuring dữ liệu

```javascript
    const [winner, ...losers] = poniesInRace;
    // poniesInRace là một mảng chứa nhiều pony
    // 'winner' sẽ chứa pony đầu tiên
    // và 'losers' sẽ là một mảng của các phần tử còn lại
```

## Toán tử Spread ...

Trong khi Rest sử dụng để cấu thành nhiều biến riêng biệt thành một mảng đơn lẻ, thì spread lại mang ý nghĩa ngược lại là phân tách 1 mảng thành các phần tử riêng lẻ.

```javascript
    const ponyPrices = [12, 3, 4];
    const minPrice = Math.min(...ponyPrices);
```

Tobe continued ...