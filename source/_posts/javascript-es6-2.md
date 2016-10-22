---
title: Những điểm mới trong ES6 (Phần 2)
date: 2016-10-22 18:55:40
tags: 
     - javascript 
     - es6
category: memo
thumbnail: '../../../../css/images/thumbnail/es6.jpeg'

---

## Class

Một trong những tính năng được mong chờ nhất trong javascript chính là class. Mặc dù trên ES5 chúng ta có thể tạo được class, tuy nhiên đó là một cách không dễ dàng đối với những người mới bắt đầu code javascript. Với ES6 mọi thứ trở nên rất dễ dàng:

```javascript
class Pony {
  constructor(color) {
    this.color = color;
  }
  toString() {
     return `${this.color} pony`;
      // 'template literals' là một tính năng rất hay khác nữa trong ES6
  }
}
const bluePony = new Pony('blue');
console.log(bluePony.toString()); // blue pony
```

<!-- more -->

class trong javascript không giống biến var, khi chúng ta bắt buộc phải khai báo nó trước khi sử dụng. Bạn sẽ nhận ra hàm rất đặc biệt là constructor. Hàm này sẽ được gọi khi chúng ta dùng toán tử new để khởi tạo đối tượng. 
Tương tự như các ngôn ngữ khác, ES6 cho phép khai báo static bên trong class. Với hàm hoặc thuộc tính được khai báo static, chúng ta có thể truy cập trực tiếp mà không cần khởi tạo đối tượng.

```javascript
class Pony {
  static defaultSpeed() {
    return 10;
  }
}
const speed = Pony.defaultSpeed();
```

Nếu bạn là tín đồ của hướng đối tượng, chắc chắn bạn sẽ mong muốn các tính năng khác như set, get, kế thừa ... ES6 cung cấp đầy đủ điều đó. Khi chúng ta muốn get/set:

```javascript
class Pony {
  get color() {
    console.log('get color');
    return this._color;
  }
  set color(newColor) {
    console.log(`set color ${newColor}`);
    this._color = newColor;
  }
}
const pony = new Pony();
pony.color = 'red';
// 'set color red'
console.log(pony.color);
// 'get color'
// 'red'
```

Kế thừa trong ES6 như sau:

```javascript
class Animal {
  speed() {
    return 10;
  }
}
class Pony extends Animal {
}
const pony = new Pony();
console.log(pony.speed()); // 10, as Pony kế thừa method lớp cha
```

Bạn có thể override method cha như bất kỳ ngôn ngữ hướng đối tượng thông thường nào khác.

```javascript
class Animal {
  speed() {
    return 10;
  }
}
class Pony extends Animal {
  speed() {
    return super.speed() + 10;
  }
}
const pony = new Pony();
console.log(pony.speed()); // 20, as Pony overrides the parent method
```

Javascript có vẻ đã trở nên rất giống Java khi chúng ta cũng có thể sử dụng từ khóa super để gọi các hàm của lớp cha. 

##  Promises

Promise không phải tính năng quá mới mẻ, nó đã có trong Angular1 và nếu bạn đã từng code Angular2 thì chắc không quá xa lạ với khái niệm này. 
Mục đích của Promise là làm đơn giản việc lập trình không đồng bộ (Asynchronous). Chúng ta thường sử dụng callback để xử lý kết quả trả về trong ES5 khi muốn thực hiện sử lý không đồng bộ như AJAX. Promise giúp chúng ta làm phẳng code, dễ dàng trong việc đọc và bảo trì code hơn. Hãy xem ví dụ sau khi thực hiện với callback và với Promise.

Với callback:

```javascript
getUser(login, function (user) {
  getRights(user, function (rights) {
    updateMenu(rights);
  });
});
```

Với Promise:

```javascript
getUser(login)
  .then(function (user) {
    return getRights(user);
  })
  .then(function (rights) {
    updateMenu(rights);
  })
```

Mặc định Promise có thể hiểu là một đối tượng **_thenable_** nghĩa là nó có hàm _then_ . Hàm này sẽ nhận hai tham số, một xử lý khi thành công và một cho thất bại. Promise có 3 thành phần sau:
* pending: Khi promise chưa hoàn thành
* fulfilled: Khi promise thực hiện thành công
* rejected: Khi promise thực hiện thất bại

Khi promise được fulfilled thì hàm success sẽ được gọi, và ngược lại khi rejected thì hàm xử lý lỗi sẽ được gọi.
Vậy làm thế nào để gọi Promise? Chúng ta chỉ đơn giản khởi tạo một đối tượng có tên Promise. Hàm này sẽ nhận 2 tham số callback như đã nói ở trên, một cho thành công, một cho thất bại.

```javascript
const getUser = function (login) {
  return new Promise(function (resolve, reject) {
      // async stuff, like fetching users from server, returning a response
      if (response.status === 200) {
        resolve(response.data);
      } else {
        reject('No user');
      }
  });
};
```

Khi khai báo như trên xong chúng ta có thể sử dụng getUser với hàm then. Trong trường hợp chỉ muốn xử lý thành công chúng ta có thể bỏ qua biến reject như dưới.

```javascript
getUser(login)
  .then(function (user) {
    console.log(user);
  })
```

Điểm hay của Promise như đã nói ở trên đó là nó làm phẳng code của chúng ta. Trong trường hợp chúng ta có nhiều callback lồng nhau thông thường chúng ta có thể viết:

```javascript
getUser(login)
  .then(function (user) {
    return getRights(user) // getRights is returning a promise
      .then(function (rights) {
        return updateMenu(rights);
      });
  })
```

Nhưng với promise chúng ta có thể viết một cách đẹp đẽ hơn thế.

```javascript
getUser(login)
  .then(function (user) {
    return getRights(user) // getRights is returning a promise
  })
  .then(function (rights) {
    return updateMenu(rights);
  });
```

Một điểm thú vị nữa đó là hàm xử lý lỗi, chúng ta có thể viết riêng cho từng promise, nhưng cũng có thể gộp chung cho một chuỗi promise như sau:
Xử lý lỗi cho từng promise

```javascript
getUser(login)
  .then(function (user) {
    return getRights(user);
  }, function (error) {
      console.log(error); // xử lý cho getUser failed
      return Promise.reject(error);
  })
  .then(function (rights) {
    return updateMenu(rights);
  }, function (error) {
      console.log(error); // xử lý cho getRights failed
      return Promise.reject(error);
  })
```

Xử lý lỗi chung có các promise với catch

```javascript
getUser(login)
  .then(function (user) {
    return getRights(user);
  })
  .then(function (rights) {
    return updateMenu(rights);
  })
  .catch(function (error) {
    console.log(error); // Xử lý lỗi chung khi có một xử lý trong chuỗi lỗi
  })
```

## Arrow functions

Đây là một trong những tính năng cá nhân mình thích nhất của ES6. Sử dụng hàm mũi tên sẽ vô cùng tiện dụng trong nhiều trường hợp. Quay trở lại với ví dụ về Promise phía trên ta có:

```javascript
getUser(login)
  .then(function (user) {
    return getRights(user); // getRights is returning a promise
  })
  .then(function (rights) {
    return updateMenu(rights);
  })
```

Khi sử dụng cú pháp arrow chúng ta có thể viết lại như sau:

```javascript
getUser(login)
  .then(user => return getRights(user))// getRights is returning a promise
  .then(rights => return updateMenu(rights))
```

Rất đẹp phải không nào. 
Tôi đã không nói nên lời khi lần đầu tiên sử dụng cú pháp này trong dự án của mình. Bởi vì nó làm cho code trở nên vô cùng đẹp. Chúng ta phải chú ý là khi giá trị trả về là một block code chúng ta cần có ngoặc đơn như sau:

```javascript
getUser(login)
  .then(user => {
    console.log(user);
    return getRights(user);
  })
  .then(rights => updateMenu(rights))
```

Cú pháp arrow không chỉ giúp cho việc code trở nên đơn giản hơn mà nó còn có một đặc điểm rất quan trọng sau. 
Lấy ví dụ đoạn code sau:

```javascript
var maxFinder = {
  max: 0,
  find: function (numbers) {
  // let's iterate
  numbers.forEach(
      function (element) {
          // if the element is greater, set it as the max
          if (element > this.max) {
            this.max = element;
          }
      });
  }
};
maxFinder.find([2, 3, 4]);
// log the result
console.log(maxFinder.max);
```

Đoạn code trên sẽ không chạy như bạn mong đợi. Bởi vì this trong javascript sẽ trỏ tới đối tượng đang được tương tác.Tuy nhiên trong trường hợp này khi sử dụng hàm anonymous trong hàm forEach thì biến this sẽ được trỏ tới Window. Tất nhiên chúng ta có thể fix vấn đề này một cách đơn giản bằng việc gán lại biến this cho một biến khác để đảm bảo trỏ đúng tới đối tượng mong muôn:

```javascript
var maxFinder = {
  max: 0,
  find: function (numbers) {
  // let's iterate
  var self = this;
  numbers.forEach(
      function (element) {
          // if the element is greater, set it as the max
          if (element > self.max) {
            self.max = element;
          }
      });
  }
};
maxFinder.find([2, 3, 4]);
// log the result
console.log(maxFinder.max);
```

Hoặc có thể sử dụng hàm bind để gắn con trỏ this vào.

```javascript
var maxFinder = {
  max: 0,
  find: function (numbers) {
  // let's iterate
  numbers.forEach(
      function (element) {
          // if the element is greater, set it as the max
          if (element > this.max) {
            this.max = element;
          }
      }.bind(this));
  }
};
maxFinder.find([2, 3, 4]);
// log the result
console.log(maxFinder.max);
```

Thậm chí có thể truyền trực tiếp biến this như một tham số thứ 2 cho hàm forEach

```javascript
var maxFinder = {
  max: 0,
  find: function (numbers) {
  // let's iterate
  numbers.forEach(
      function (element) {
          // if the element is greater, set it as the max
          if (element > this.max) {
            this.max = element;
          }
      }, this);
  }
};
maxFinder.find([2, 3, 4]);
// log the result
console.log(maxFinder.max);
```

Với ES6 mọi thứ trở nên dễ dàng hơn với arrow function:

```javascript
const maxFinder = {
  max: 0,
  find: function (numbers) {
      numbers.forEach(element => {
          if (element > this.max) {
            this.max = element;
          }
      });
  }
};
maxFinder.find([2, 3, 4]);
// log the result
console.log(maxFinder.max);

```

##  Sets and Maps

Với ES6 chúng ta có thêm các kiểu dữ liệu collection là Set và Map. Bạn nào từng làm Java sẽ thích điều này. Ví dụ về Map:

```javascript
const cedric = { id: 1, name: 'Cedric' };
const users = new Map();
users.set(cedric.id, cedric); // adds a user
console.log(users.has(cedric.id)); // true
console.log(users.size); // 1
users.delete(cedric.id); // removes the user
```

Ví dụ về Set

```javascript
const cedric = { id: 1, name: 'Cedric' };
const users = new Set();
users.add(cedric); // adds a user
console.log(users.has(cedric)); // true
console.log(users.size); // 1
users.delete(cedric); // removes the user
```

Cuối cùng như bao collection khác, chúng ta có thể loop thông qua for ... of

```javascript
for (let user of users) {
  console.log(user.name);
}
```

## Template literals

Nối string luôn là một chuyện rườm rà trong javascript. Chúng ta thông thường sẽ làm:

```javascript
const fullname = 'Miss ' + firstname + ' ' + lastname;
```

Nhưng với chức năng template literal thì mọi chuyện sẽ trở nên dễ dàng hơn nhiều

```javascript
const fullname = `Miss ${firstname} ${lastname}`;
```

Và thậm chí nó còn trở nên hữu ích hơn rất nhiều khi nó hỗ trợ chức năng multi line.

```javascript
const template = `<div>
  <h1>Hello</h1>
</div>`;
```

## Modules

Module là một chức năng còn thiếu của javascript cho tới ES5. Khi chúng ta muốn tổ chức code, chúng ta đều phải sử dụng các third party như [RequireJs](http://requirejs.org/).
ES6 cung cấp thêm chức năng module và các cú pháp cho phép chúng ta import/export thông tin từ các modules. Mặc định mỗi file là một module riêng biệt trong ES6. 

File races_service.js
```javascript
export function bet(race, pony) {
  // ...
}
export function start(race) {
  // ...
}
```

Với từ khóa export chúng ta export 2 hàm, và ở một file khác:

```javascript
import { bet, start } from './races_service';
// chúng ta có thể dùng
bet(race, pony1);
start(race);
```

Ngoài ra chúng ta có thể import tất cả các thành phần export bằng lệnh

```javascript
import * as racesService from './races_service';
// chúng ta có thể dùng
racesService.bet(race, pony1);
racesService.start(race);
```

Nếu module chỉ export một function, class thì chúng ta có thể sử dụng keyword default như sau

```javascript
// pony.js
export default class Pony {
}
// races_service.js
import Pony from './pony';
```

Chú ý khi import với keyword default chúng ta không cần dấu ngoặc đơn nữa. Chú ý một module chỉ cho phép một default keyword.

## Kết luận

Đây chỉ là tóm tắt những điểm cơ bản. ES6 còn rất nhiều điểm thú vị bên trong. Bạn có thể đọc thêm ở một số link sau:

* [Understanding ECMAScript 6](https://leanpub.com/understandinges6)
* [exploringjs](http://exploringjs.com/)