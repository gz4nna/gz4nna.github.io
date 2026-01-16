# 跟著官網教程學習Angular
(v20版本)
## 組件

`Angular` 中的組件由三個主要部分組成:
- TS類
- HTML模板
- CSS樣式

比如一個比較基礎的樣子就是
```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    Hello
  `,
  styles: `
    :host {
      color: blue;
    }
  `,
})
export class App {}
```
這段大致上就相当于
```html
<p style="color:blue">Hello</p>
```
會在屏幕上顯示
<p style="color:blue">Hello</p>
這也很好理解,`template`中就是顯示的文本,`styles`裏則替代了原本的`css`

### TS類
組件的邏輯和行爲都是在組件的TS類中被定義的
比如說在上面的例子裏面,將對`App`類的定義換成
```typescript
export class App {
  city = 'San Francisco';
}
```
這樣就可以去HTML模板中調用`App`類中的屬性了
使用雙大括弧`{{}}`去包裹屬性進行引用,另外這個符號還可以對内部表達式進行計算
```typescript
template: `Hello {{ city }}, {{ 1 + 1 }}`,
```

### 組件引用
組件由類確定,對組件的引用也就是對類的引用
在這一段中:
```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app-user',
  template: `
    Username: {{ username }}
  `,
})
export class User {
  username = 'youngTech';
}

@Component({
  selector: 'app-root',
  template: `
    <section>
      <app-user />
    </section>
  `,
  imports: [User],
})
export class App {}
```
在`App`中直接引用了其他的類(`User`),可以看到在`User`中定義了一個選擇器叫做`app-user`,那麽只需要在`App`中的`imports`中添加對他的引用,就可以將這個組件(選擇器)當作自己的使用了
這一段編譯之後的html為下面這樣:
```html
<app-root ng-version="20.1.0-rc.0">
	<section>
		<app-user> Username: youngTech </app-user>
	</section>
</app-root>
```

### 組件控制流
#### @if
組件中可以使用`@if`來完成條件判斷
```typescript
import {Component} from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
  @if (isServerRunning) {
    <span>Yes, the server is running</span>
  }
  @else if(isLoggedIn){
    <p>Welcome back, Friend!</p>
  }
  @else{
    <span>No, the server is paused</span>
  }    
  `,
})
export class App {
  isServerRunning = false;
  isLoggedIn = true;
}
```
與之搭配的是`@else if`以及`@else`
#### @for
循環控制使用的是`@for`