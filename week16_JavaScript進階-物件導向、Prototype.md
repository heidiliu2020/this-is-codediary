###### tags: `JavaScript` `物件導向` `prototype`
# [week 16] JavaScript 進階 - 物件導向 & Prototype

> 本篇為 [[JS201] 進階 JavaScript：那些你一直搞不懂的地方](https://lidemy.com/p/js201-javascript) 這門課程的學習筆記。如有錯誤歡迎指正！

```
學習目標：

 P1 你知道 Prototype 在 JavaScript 裡是什麼
 P1 你知道大部分情況下 this 的值是什麼
 P2 你知道物件導向的基本概念（類別、實體、繼承、封裝）
```

---

## 什麼是物件導向？

其實我們在上一篇 [Closure & Scope Chain 筆記](https://hackmd.io/@Heidi-Liu/note-js201-closure)中，就有稍微提到物件導向的相關觀念：

```javascript=
function createWallet(initMoney) {
  var money = initMoney;
  return {
    add: function(num) {
      money += num;
    },
    deduct: function(num) {
      if (num >= 10) {
        money -= 10;
      } else {
        money -= num
      }
    },
    getMoney() {
      return money;
    }
  }
}
var myWallet = createWallet(99);
myWallet.add(1);
myWallet.deduct(100);
console.log(myWallet.getMoney()); // 90
```

在物件導向的世界，我們以呼叫 function 的方式進行操作，會比較像是在使用物件的形式。例如 myWallet.add() 這句的意思，就像是對 myWallet 這個物件做一些操作。

像這樣透過塑造物件，就可以不需一直 Call function，也會使程式更模組化一些，並且能夠隱藏內部資訊。

### 建立物件實體（Object instance）

根據 [MDN](https://developer.mozilla.org/zh-TW/docs/Learn/JavaScript/Objects/Object-oriented_JS) 說明，其實物件導向（Object-oriented）的基本概念就是：「採用物件（Objects）來模塑真實的實物世界」。

也就是說，我們可以在程式中透過 Objects（物件）來塑造其模型，而物件能夠存放其相關資料與程式碼，並且能使用方式（Method）來進行存取。

舉個簡單的例子，以一隻狗來說，可能會有許多基本資訊，像是毛色、身高、名字、性別等，透過這些抽象概念或特質，我們能夠建立一個「Dog」範本，作為代表狗的物件類別（class）。

接著我們可以從這個類別建立物件實體（Object instance），也就是該透過執行類別的「建構子函式（Constructor Function）」建立物件，而這段過程就稱為實體化（Instantiation）。

```
// 透過類別中的建構子將物件實體化
class -> Constructor -> Object instance
```

### Constructor Function & new

前面我們提到，JavaScript 是使用類別（class）中的建構子函式（Constructor Function）來定義物件與功能。

但實際上，JavaScript 程式本身並沒有 class 可以使用，是到了 ES6 以後才出現。簡單來說，class 其實是 ES6 提供的語法糖，底層機制仍然是 JavaScript 的原型（prototype）。

接著同樣以建立 Dog 物件類別為例：

- class 後面要接名稱，表示要「建立 Dog 這個 class」，第一個字必須為大寫
- 接著透過 `new` 指令來實體化，建立出物件實體（Object instance）
- class 中的 `constructor` 用來定義物件，也就是在建立時會呼叫 `constructor` 來執行內部的程式碼，目的是初始化 instance
- 透過 instance 傳入參數，再由 `constructor` 接收，可設定 Dog 的屬性 `name`

```javascript=
class Dog {
  constructor(name) {
    this.name = name;
    console.log(this);
    console.log(this.name);
  }
}

var a = new Dog('dog A');
var b = new Dog('dog B');

// Dog {name: "dog A"}
// dog A
// Dog {name: "dog B"}
// dog B
```

- constructor 也可用透過呼叫 function 來定義物件的方法
  - 在 constructor 中，有設定就會有取得。例如：setName 會被稱為 setter，getName 則是 getter
  - 透過這種形式，就不需直接操作 class 內部的值
- this 在 class 的用途：誰呼叫它就指向呼叫它的
  - 例如 `a.sayHello();`：代表 a 透過 sayHello 呼叫 this，所以這個 this 是 a

```javascript=
class Dog {
  constructor(name) {
    // setter
    setName(name) {
      this.name = name;
    }
    // getter
    getName() {
      return this.name;
    }
    sayHello() {
      console.log('hello');
  }
}

var a = new Dog('dog A');
console.log(a.name)    // dog A
a.sayHello();      // hello

var b = new Dog('dog B');
console.log(b.name)    // dog B
b.sayHello();      // hello
```

此外，藉由 new 指令建立出 a 和 b 兩個物件，這兩個物件是完全獨立的物件，且都事先具備 Dog 這個 class 的所有屬性。

這其實和物件導向的三大特性有關，這部分我們後面會再提到：

- 封裝（Encapsulation）
- 繼承（Inheritance）
- 多型（Polymorphism）

## Prototype Chain 原型鍊

在建立物件實體（Object instance）之後，接著需透過原型（Prototype）機制來相互繼承功能。

前面有提到，class 其實是 ES6 提供的語法糖，底層機制仍然是 JavaScript 的原型（prototype）。因此，即使是在不使用 class 的情況下，我們一樣可以透過 function 和 prototype 來做到 class（類別）的功能。

### ES5 寫法

若將前面的範例改成 ES5 寫法，也就是替換掉 constructor 和 new，如下方程式碼：

```javascript=
function Dog(name) {
  var myName = name;
  return {
    getName: function () {
      return myName
    },
    sayHello: function () {
      console.log('say hello!')
    }
  }
}

var a = Dog('dog A');   // say hello!
a.sayHello();
var b = Dog('dog B');   // say hello!
b.sayHello();
console.log(a.sayHello === b.sayHello);   // false
```

會發現 a.sayHello 和 b.sayHello 兩個同樣的 function 卻不是一樣的東西，代表程式在背後其實儲存了兩次。原因在於 class 中是不同的 instance，造成 this 指向不同。

但這其實會產生一個問題，如果今天有一萬隻狗，就會儲存一萬次 function，明明都是在處理同樣的事情，應該改成使用同一個 function 去跑不同的 instance 即可。


### 以 `.prototype` 連結 function

在 JavaScript 機制中，有個 `.prototype` 語法能夠連結 function，如下方程式碼：

- 使用 `Dog.prototype.sayHello` 將建構 function 新增一個 sayHello 的屬性
- new 出來的物件皆可存取 sayHello 這個屬性

```javascript=
function Dog(name) {    // 等於 constructor
  this.name = name;
}

Dog.prototype.getName = function () {
  return this.name;
}

Dog.prototype.sayHello = function () {
  console.log('say hello!');
}

var a = new Dog('dog A');
var b = new Dog('dog B');
console.log(a.sayHello === b.sayHello);  // true
```

兩個 function 就會是相同的，因為 a 和 b 都是 prototype 上面的 function。如此一來，我們就可以使用同一個 function 去跑不同的 instance，透過這個方式實作 JavaScript 的物件導向。

### `__proto__` & `prototype`

所以 prototype 是什麼？在 [ECMA5.1](https://www.ecma-international.org/ecma-262/6.0/#sec-terms-and-definitions-prototype) 標準中，定義原型（prototype）是為其他對象提供共享屬性的對象：

> object that provides shared properties for other objects

也就是說，原型（prototype）的本質，就是能夠分享自己的屬性給其他物件使用，而所有的物件都有原型（prototype）屬性。

接著舉一個簡單的物件為例：

```javascript=
const obj = { name: "Dog" };
console.log(obj);
```

可以看到 obj 物件的原型物件，具有的 `__proto__`、`constructor` 等特性，這些都是由原型物件分享給 obj 物件使用的特性：

![](https://i.imgur.com/jwWgnE9.png)

接著來說明 `prototype` 和 `__proto__` 之間的差異：

#### `prototype`（顯性原型）
- 用來實現基於原型的繼承與屬性的共享
- 例如：用來指定屬性或 function
#### `__proto__`（隱性原型）
- 構成原型鏈，同樣用於實現基於原型的繼承
- 例如：繼承資料
- 由 new 指令建立的物件，`__proto__` 屬性會指向該原型物件的 `prototype`
- 也就是說，當我們想找 obj 物件中的 X 屬性時，如果在 obj 中找不到，就會繼續沿著 `__proto__` 往下一層查找

參考資料：[js中__proto__和prototype的區別和關係？](https://www.zhihu.com/question/34183746)

接著繼續沿用剛才的 Dog 範例：

```javascript=
function Dog(name) {    // 等於 constructor
  this.name = name;
}

Dog.prototype.getName = function () {
  return this.name;
}

Dog.prototype.sayHello = function () {
  console.log('say hello!');
}

var a = new Dog('dog A');
var b = new Dog('dog B');
console.log(a.__proto__);
console.log(b.__proto__);
// Dog { getName: [Function], sayHello: [Function] }
// Dog { getName: [Function], sayHello: [Function] }
```

會發現 `a.__proto__` 和 `b.__proto__` 其實就代表 Dog.prototype，會得到相同結果：

```javascript=
console.log(a.__proto__ === Dog.prototype)
// true
```

也映證我們前面所說，class 底層其實還是 prototype（原型）這件事，只是寫法不同。

而當我們執行 `a.sayHello()` 的時候，程式會沿著原型鏈尋找 sayHello 屬性，參考過程如下：

```javascript=
a.sayHello()

1. a　有沒有 sayHello
2. a.__proto__　有沒有 sayHello
3. a.__proto__.__proto__　有沒有 sayHello
4. a.__proto__.__proto__.__proto__　有沒有 sayHello
5. 找到最後剩下 null，也就是最頂層

a.__proto__ = Dog.prototype
a.__proto__.__proto__ = Dog.prototype.__proto__ = Object.prototype
a.__proto__.__proto__.__proto__ = null
```

像這樣透過 `__proto__` 不斷串起來的鏈，就稱作原型鏈（Prototype Chain），其實概念類似於之前提過的 Scope Chain。透過這條原型鏈，就可以引用或繼承自己物件沒有的屬性。

```javascript=
// Prototype Chain
a --> Dog.prototype --> Object.prototype --> null
// 等同於
a -> a.__proto__ --> a.__proto__.__proto__ --> a.__proto__.__proto__.__proto__
```

function 的 `__proto__` 其實也是相同道理：

### hasOwnProperty()

- 用來判斷某個物件是否含有指定的自身屬性
- 以 Dog 例子來說，可用 `a.hasOwnProperty('sayHello')` 來判斷 sayHello 是存在於 instance 還是該原型鏈中

```javascript=
console.log(a.__proto__.hasOwnProperty('sayHello'));
// true
console.log(a.hasOwnProperty('sayHello'));
// false
```

### instanceof

- 用來判斷 A 物件是否為 B 的實例，比較的是原型（prototype）：

```javascript=
console.log(a instanceof Dog);  // true
```

#### 比較：instanceof vs typeof
- typeof：用來判斷參數是什麼型別，回傳值是 data type

```javascript=
console.log(typeof 123)  // number
```

## new 扮演的角色

在理解 new 在背後做了什麼是之前，先來看這個例子：

```javascript=
function test() {
  console.log(this)
}
test();
```

會發現 this 其實是非常大的值，裡面包含許多東西：

![](https://i.imgur.com/kqTUu5D.png)

### `.call()`：另一種呼叫 function 的方式

此外，function 還有一種呼叫方式叫做 `.call()`，如果我們 call 來呼叫 test，並在括號內帶入值，將會改變 function 結果：

```javascript=
function test() {
  console.log(this)
}

test.call('123');  // [String: '123']
test.call({});     // {}
```

也就是說，若使用 `.call()` 呼叫 function，會將傳入的東西設定為 this 的值。

接著我們可以透過「`.call()` 第一個參數代表 this 指向」這個概念，試著自己建立一個 new 的模型：

```javascript=
function Dog(name) {
  this.name = name;
}

Dog.prototype.getName = function () {
  return this.name;
}

Dog.prototype.sayHello = function () {
  console.log('hello', this.name);
}

// 使用 new 方法
var a = new Dog('dog a');

// 透過 function 實作 new 方法
var b = newDog('dog b')

function newDog(name) {
  var obj = {};                 // 先建立一個空物件 obj
  Dog.call(obj, name);          // 呼叫 constructor，this 會指向這個物件
  obj.__proto__ = Dog.prototype;  // 建立物件的原型鏈
  return obj;                   // 回傳完成的物件 obj
}

a.sayHello();   // hello dog a
b.sayHello();   // hello dog b
```

透過 function 實作 new 方法，上述的程式碼步驟如下：

1. 先建立一個空物件 obj
2. 呼叫 constructor，然後把 Dog 利用 `.call()` 帶入空物件，並設定參數：`Dog.call(obj, name)`，第一個參數代表 this 指向 obj，第二個參數代表要帶入的值
3. 接著設定方法：建立物件的原型鏈，針對 obj 的 `.__proto__` 等同於 Dog 的 `.prototype` 即可完成關聯
4. 回傳完成的物件 obj

---

## 物件導向的繼承：Inheritance

當其他類別需要用到共同屬性時，不需再重新建立 class 的各種屬性，可以利用繼承的方法，來直接存取父層的屬性。

例如狗本身有名字有動作，有自己的屬性與方法。而黑狗也屬於一種狗，黑狗也有同樣的屬性與方法，只是會有些微差異，這種情況我們可以透過繼承，也就是讓黑狗 class 直接使用狗 class，就不需再另外建立。

如下方的範例：

```javascript=
class Dog {
  constructor(name) {
    this.name = name;
  }
  sayHello() {
    console.log(this.name);
  }
}
// 繼承 Dog class
class BlackDog extends Dog {
  test() {
    console.log('test', this.name);
  }
}

const black = new BlackDog('hello');
black.sayHello();   // hello
```

### 修改子層 class 的 constructor

如果想要在子層的 class 更改 constructor 屬性時，必須先呼叫 `super()`，並把父層 class constructor 需要的參數傳進去。否則就只會初始化子層的 constructor，不會有繼承的作用。

透過 `super()` 來呼叫父層 class 的 constructor，才能連同父層的 constructor 一併初始化，並接收初始化的值：

```javascript=
class Dog {
  constructor(name) {
    this.name = name;
  }
  getName() {
    return this.name;
  }
  sayHello() {
    console.log('Hello, ' + this.name);
  }
}

class BlackDog extends Dog {    // 繼承
  constructor(name) {
    super(name);      // 繼承後在 constructor 一定要使用 super 接收資料
    this.sayHello();  // 當 BlackDog 被建立時，就打招呼
  }
  test() {
    console.log('test!', this.name);
  }
}

const a = new Dog('dog a');
a.sayHello();
// Hello, dog a.

const black = new BlackDog('I am black dog'); 
// Hello, I am black dog
```

透過子層繼承父層類別可延伸多種型態，也就是同樣的方法名稱會有多種行為，使用上也增加彈性，這其實就是物件導向的特性之一：多型（Polymorphism）。

---

## 結語

在學習 JavsScript 之前，一直以為物件導向和 this 是能夠畫上等號的（三個的那種）。直到實際學到物件導向以後，才瞭解到物件導向中有許多觀念，其實和在之前學到的 Hoisting、Closure 有很大的關聯。此外，物件導向其實應用在許多現代的程式語言，以物件導向的方向進行開發。

物件導向程式的寫法，基本上可分為三部分：

1. 定義物件類別（class）。例如：`class Dog`
2. 定義物件類別中的屬性與方法。例如：可使用 `dog.name` 存取屬性，使用 `dog.sayHello()` 存取方法
3. 定義物件之間的行為，也就是主程式

之所以需要物件導向，最重要的目的就是把資料（屬性）與函式（方法）結合在一起，定義出物件模型，這麼做有幾個優點：

- 便於重複使用程式碼
- 能夠隱藏程式內部資訊
- 透過模組化來簡化主程式邏輯

而這些概念，其實也就是先前談到有關物件導向的三大特性，並且三者具有次序性，沒有封裝就不可能有繼承、沒有繼承就不可能有多型：

- 封裝（Encapsulation）：
  - 藉由把程式包成類別，能夠隱藏物件內容
  - 避免程式間互相干擾，也利於後續維護
- 繼承（Inheritance）：
  - 子層能夠繼承使用父層的屬性和方法，並且加以微調
  - 能夠重複使用程式碼
- 多型（Polymorphism）：
  - 父層可透過子層衍伸成多種型態，接著子層可藉由覆寫父層的方法來達到多型
  - 可增加程式架構的彈性與維護性

藉由瞭解什麼是物件導向，為什麼需要物件導向以後，對整體架構似乎又更加清楚一些。過程中也查了許多資料，在碰到新的名詞時總會感到慌張，像是 constructor（建構子）、prototype（原型）、instance（實例）等等，其實只要能夠先瞭解定義是什麼，就不難繼續理解整體架構。

最後，在找相關資料的時候，有在這篇[網誌](https://igouist.github.io/post/2020/07/oo-5-polymorphism/)中，看到使用泡麵的例子來比喻物件導向，因為還蠻喜歡的也記錄在這裡：

1. 由泡麵工廠製作麵和醬包，並包裝在一起，我們可以直接買來享用
2. 我們可以在泡麵中自己加料，或是不用泡的改用炒的
3. 同樣都是泡麵，卻能夠實作出不同的口味

參考資料：

- [該來理解 JavaScript 的原型鍊了](https://blog.huli.tw/2017/08/27/the-javascripts-prototype-chain/)
- [[week 16] JavaScript 進階 - 淺談物件導向 & this](https://hugh-program-learning-diary-js.medium.com/%E5%89%8D%E7%AB%AF%E4%B8%AD%E9%9A%8E-js%E4%BB%A4%E4%BA%BA%E6%90%9E%E4%B8%8D%E6%87%82%E7%9A%84%E5%9C%B0%E6%96%B9-%E7%89%A9%E4%BB%B6%E5%B0%8E%E5%90%91-cdea0e3266ee)
- [JavaScript: Object-oriented JavaScript, Prototype Chain & This](https://nicolakacha.coderbridge.io/2020/10/01/oop-prototype-this/)