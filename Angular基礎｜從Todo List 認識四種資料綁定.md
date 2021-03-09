###### tags: `Angular`
# Angular 基礎｜從 Todo List 認識四種資料綁定

本篇主要在認識四種資料綁定，使用 [todoMVC](https://github.com/tastejs/todomvc) 提供的模板樣式練習。

## 資料綁定 Data Binding

> 斯斯有兩種，資料綁定有四種。

下圖出自於 [Angular 官網](https://angular.tw/guide/architecture-components)：

![](https://i.imgur.com/avqYqaV.png)

資料綁定（Data Binding）是 Angular 用來協調 Component（TypeScript）與 Template（HTML）互相傳遞資料的機制，依照方向性的不同，可分為四種綁定方式：

- 內嵌綁定/插值（Interpolation）
    - `{{value}}`
- 屬性綁定（Property Binding）
    - `[propertyName] = 'value'`
    - `[attr.attributeName] = 'value'`
- 事件綁定（Event Binding）
    - `(eventName) = 'someMethod($event)'`
    - `(eventName.key) = 'someMethod($event)'`
- 雙向綁定（Two-Way Binding）
    - `[(ngModel)] = 'property'`

前三種均屬於單向綁定（One-Way Binding）；第四種雙向綁定，則是屬性綁定加上事件綁定的組合。

<br/>

## 內嵌綁定（插值）

- 單向性：value 改變 HTML 跟著變
  - Component 變數→Template 值
- 使用方法：直接在 HTML 中插入變數 `{{value}}`

以下為範例：

- app.component.html：在 Template 在要嵌入的地方加上`{{雙大括號}}`

```htmlmixed=
<h1>{{title}}</h1>
```

- app.component.ts：變數 title 一旦改變，會連動更新 HTML h1 的內容

```typescript=
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'Todo List';
}
```

## 屬性綁定

- 單向性：value 改變會影響 property，HTML 跟著改變
  - Component 變數→Template 值
- 使用方法：在 HTML 中的屬性加上 `[property]="value"`

以下為範例：

- todo-list.component.ts：新增一個 inputHint 變數

```typescript=
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class TodoListComponent implements OnInit {
  inputHint = 'Add New Todo Here...';
}
```

- todo-list.component.html：變數 inputHint 一旦改變，會連動更新 input 的 placeholder 內容

```htmlmixed=
<input class="todo-input" [placeholder]="inputHint" autofocus>
```

結果如下：

![](https://i.imgur.com/AoZhrIi.png)

根據使用的 property 又分以下幾種，常用於根據綁定 value 布林值決定顯示或移除：

- Emelent property：例如綁定路徑、是否鎖住按鈕等等

```htmlmixed=
<img [src]="imageUrl">

<button [disabled]="!isValid">Save</button>
```

- Component property：使用對象為 Component，常用於傳入資料

```htmlmixed=
<hero-detail [hero]="currentHero"></hero-detail>
```

- Class property：CSS 類別

```htmlmixed=
<li [class.completed]="todo.done"></li>
```

- Attribute property：CSS 屬性

```htmlmixed=
[attr.attributeName] = "statement"

 <label [attr.contenteditable]="!todo.done"></label>
```

- Style property：CSS 風格

```htmlmixed=
<button [style.color] = "isValid ? 'green': 'red'">Save</button>
```

- Directive property： ngClass 和 ngStyle 是 Angular 提供動態設置 CSS 的方法

```htmlmixed=
<li [ngClass]="{completed: todo.done}"></li>
```

## 事件綁定

- 單向性：一旦觸發指定 event，就會呼叫 TypeScript 中的 someMethod 方法
  - Template 發送事件→Component 呼叫方法
- 使用方法：在 HTML 中的加入 `(event)="someMethod()"`

以下為範例：

- app.component.ts：宣告 onClick() 方法

```typescript=
export class AppComponent {
  onClick(value: string): void {
      alert("Hello " + value);
  }
}
```

- app.component.html：在 HTML 設定 Click 事件，觸發時就會呼叫 TypeScipt 的 onClick 方法

```htmlmixed=
<button (click)="onClick('World')">Click Me!</button>
```

結果如下：

<iframe src="https://codesandbox.io/embed/event-binding-6gs11?fontsize=14&hidenavigation=1&theme=dark"
     style="width:80%; height:200px; border:0; border-radius: 4px; overflow:hidden;"
     title="Event Binding"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

   
   
## 雙向綁定：屬性 + 事件

> [()] 符號口訣：Banana is in the Box!（香蕉在箱子裡）

- 雙向性：Component 或 Template 其中一方的值改變，另一方也會跟著變
  - Component ⇄ Template 
- 使用方法：在 HTML 加入 `[(ngModel)]` 語法，使用前需要先在 AppModule 引用 FormsModule
- 通常用於 `<input>`、`<textarte>` 等表單元素

以新增 Todo 功能為例：

- todo-list.component.ts：宣告一個 todos 陣列、變數 todo 和 addTodo() 方法

```typescript=
import { Component } from '@angular/core';

@Component({
  selector: 'app-todo-list',
  templateUrl: './todo-list.component.html',
  styleUrls: ['./todo-list.component.css']
})
export class TodoListComponent {
  todos = [];
  todo = '';
  
  // 新增 Todo 功能
  addTodo() {
    if (!this.todo.trim()) { return; }    // input 為空則返回
    this.todos.push({     // 把狀態暫存在 model
      text: this.todo,  // todo 內容
      done: false       // 是否已完成
    });
    this.todo = '';    // 新增完就清空
  }
}
```

- todo-list.component.html：分別在 input 設定 Enter 事件，以及 button 設定 Click 事件，觸發事件時就會呼叫 addTodo() 方法

```htmlmixed=
<input
  class="todo-input" [placeholder]="inputHint" 
  [(ngModel)]="todo" (keyup.enter)="addTodo()"
>
<button class="btn-add" (click)="addTodo()"></button>
```

使用 `[(ngModel)]` 語法，要記得在 app.module.ts 引入 FormsModule：

```typescript=
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';  //NgModel lives here

@NgModule({
  // ...
  imports: [
    BrowserModule, 
    FormsModule  //import the FormsModule before binding with [(ngModel)]
  ],
  
})
```

### 實作：刪除 Todo 功能

在上述範例中，我們完成了新增 Todo 功能，接著要實作刪除 Todo：

- todo-list.component.html
  - `*ngIf`: 透過判斷式控制資料顯示與否
  - `*ngFor`: 透過迴圈顯示多筆資料
  - 使用內建的 pipe: uppercase 會統一將輸出轉成大寫
  - 在 button 元素加上事件綁定 click

```htmlmixed=
<section class="main" *ngIf="todos.length > 0">
  <ul class="todo-list">
    <li *ngFor="let item of todos">
      <div class="view">
        <input class="toggle" type="checkbox">
        <label>{{item.text | uppercase}}</label>
        <button class="btn-destroy" (click)="removeTodo(item)"></button>
      </div>
    </li>
  </ul>
</section>
```

- todo-list.component.ts：觸發 click 事件時就會呼叫 removeTodo 方法

```typescript=
removeTodo(item): void {
  this.todos.splice(this.todos.indexOf(item), 1);
}
```

<br>

## 補充：淺談 NgClass 與 NgStyle

NgClass 與 NgStyle 是 Angular 提供的指令，可用來動態改變 DOM 元素的 CSS，以下和原生屬性與不同綁定方式進行比較：

- Native attributes：靜態使用單一 / 多個樣式
- Class 和 Style Bindings：動態使用單一樣式
- 使用 NgClass 和 NgStyle：動態使用單一 / 多個樣式

```htmlmixed=
<!-- Native Attributes -->
<input class="is-danger" style="border: none; color: blue">

<!-- Angular Bindings -->
<input [class.is-danger]="isDanger" [style.border]="hasborder">

<!-- ngClass -->
<input [ngClass]="{'is-danger': isDanger, 'myButton': true}">
<input [ngClass]="isDangerButton">

<!-- ngStyle -->
<input [ngStyle]="{'border': hasBorder, 'color': colorProp}">
<input [ngStyle]="hasColorBorder">
```

至於優缺點，以下方程式碼為例：

- 動態使用單一樣式
  - 可讀性高

```htmlmixed=
<li [class.completes]="todo.isDoing"
    [class.editing]="todo.isEditing">
</li>
```

- 動態使用多種樣式
  - 可一次表達多種樣式

```htmlmixed=
<li [ngClass]="{completed: todo.isDone, editing: todo.isEditing}" >
</li>
```

兩者可達成相同效果，依照需求和習慣選用即可！

參考資料：

- [Angular 4 教學 - Data Binding](https://blog.johnwu.cc/article/angular-4-%E6%95%99%E5%AD%B8-data-binding.html)
- [[功能介紹-2] 資料繫結的模版語法](https://ithelp.ithome.com.tw/articles/10193917)
- [精通Angular之NgClass和NgStyle](https://zhuanlan.zhihu.com/p/95490706)