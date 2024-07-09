# 暑期實訓 其之二

## 前言

接[上文](./DAY01.html)

此頁記錄實訓第二天内容，首先總結一下今日事項，主要概括為以下幾方面

- 補充第一天未講解的部分
- 熟悉各種組件及基本使用方法

## 主要内容

由於基本動作皆爲在組件目錄中創建一個新組件來進行一輪嘗試，因此接下來僅展示部分主要代碼

### 基本使用

#### 外部類使用

組件内容：

```typescript
import Person from "../models/Person"  
  
  
@Extend(Row) function rowBlock(){  
  .width("100%").padding({left:10,right:10,top:10,bottom:3})  
  .justifyContent(FlexAlign.SpaceBetween).border({width:{bottom:1},color:"#AAA"})  
}  
  
@Component  
struct Item{  
  @Link person : Person  
  
  build(){  
    Column(){  
      Row(){  
        Text("子组件")  
      }.width("100%").padding(10)  
      .justifyContent(FlexAlign.Center).backgroundColor("#ff65b17e")  
  
      Column(){  
        Row(){  
          Text("姓名")  
          TextInput({text:this.person.username}).onChange((val)=>{  
            this.person.username = val  
          }).flexBasis(220).backgroundColor("#FFF")  
        }.rowBlock()  
  
        Row(){  
          Text("年龄")  
          TextInput({text:this.person.age + ""}).onChange((val)=>{  
            this.person.age = parseInt(val)  
          }).flexBasis(220).backgroundColor("#FFF").type(InputType.Number)  
        }.rowBlock()  
      }.flexGrow(1)  
    }  
    .width(300).height(150).border({width:1})  
  }  
}  
  
@Component  
export default struct Test4{  
  @State person : Person = new Person()  
  
  aboutToAppear(){  
    this.person.username = "张三"  
    this.person.age = 23  
  }  
  
  build(){  
    Column(){  
      Column(){  
        Row(){  
          Text("父组件")  
        }.width("100%").padding(10)  
        .justifyContent(FlexAlign.Center).backgroundColor("#b1ae65")  
  
        Column(){  
          Row(){  
            Text("姓名")  
            TextInput({text:this.person.username}).onChange((val)=>{  
              this.person.username = val  
            }).flexBasis(220).backgroundColor("#FFF")  
          }.rowBlock()  
  
          Row(){  
            Text("年龄")  
            TextInput({text:this.person.age + ""}).onChange((val)=>{  
              this.person.age = parseInt(val)  
            }).flexBasis(220).backgroundColor("#FFF").type(InputType.Number)  
          }.rowBlock()  
        }.flexGrow(1)  
      }  
      .width(300).height(150).border({width:1})  
  
      Item({person:$person})  
  
      Item({person:$person})  
  
      Item({person:$person})  
    }.width("100%").height("100%")  
    .backgroundColor("#EEE").justifyContent(FlexAlign.SpaceEvenly)  
  }  
}
```

類内容：

```typescript
export default class Person{  
  username : string  
  age : number  
  email : string  
  
  constructor() {  
    this.username = ""  
    this.age = 0  
    this.email = ""  
  }  
}
```

其中，我們將Person類的定義單獨放在了一個新文件中，通過語句

```typescript
import Person from "../models/Person"  
```

對其進行引用
### 熟悉各種組件

#### 滾動條組件

```typescript
@Component  
export default struct Test4{  
  @State array : Array<string> =[姓名列表，已去除内容]  
  build(){  
    Column(){  
      Row(){  
        Text("滚动条")  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center)  
      Scroll(){  
        Column(){  
          ForEach(this.array, (item, index) => {  
            Row(){  
              Text(item)  
            }.width("100%").padding(10).margin(5)  
            .backgroundColor("#EEE").justifyContent(FlexAlign.Center)  
          })  
        }  
      }.scrollable(ScrollDirection.Vertical)  
      .onScroll((x, y) =>{  
        console.info(`position x:${x} y:${y}`)  
      }).flexBasis(600).scrollBar(BarState.On)  
    }.width("100%").height("100%")  
  }  
}
```

Scroll為滾動組件，可將其中内容進行滾動

默認進行竪向滾動，可通過屬性scrollable修改

滾動時觸發事件為onScroll，接受一個含雙參方法，輸入橫縱坐標

```typescript
(method) ScrollAttribute.onScroll(event: (xOffset: number, yOffset: number) => void): ScrollAttribute
```
#### 文本選擇器

```typescript
@Component  
export default struct Test6{  
  @State selectArray : Array<string> = ["张飞","关羽","刘备","赵云"]  
  @State value : string = "张飞"  
  @State selectArray1 : Array<string> = ["21","22","23","24"]  
  @State value1 : string = "21"  
  
  build(){  
    Column(){  
      Row(){  
        Text("三国人物")  
        TextPicker({range:this.selectArray,value:this.value})  
          .onChange((val) => {  
            this.value = val  
          })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Text("选取值")  
        Text(this.value)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Text("年龄")  
        TextPicker({range:this.selectArray1,value:this.value1})  
          .onChange((val) => {  
            this.value1 = val  
          })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Text("选取值")  
        Text(this.value1)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
    }.width("100%").height("100%")  
  }  
}
```

TextPicker為文本選擇器，可選中範圍内的文本，接受一個選項，常見取值範圍

```typescript
const TextPicker: TextPickerInterface
(options?: TextPickerOptions) => TextPickerAttribute
```

所選内容發生變化時觸發onChange，接受含單參方法，傳入當前選擇内容
#### 時間選擇器

```typescript
@Component  
export default struct Test7{  
  @State hour:number = 0  
  @State minute:number = 0  
  
  date:Date = new Date("2024-7-9 14:00:00")  
  
  build(){  
    Column(){  
      Row(){  
        Text("时间选择器").fontSize(20).fontWeight(700)  
      }.padding(10).width("100%").backgroundColor("#FF0")  
      .justifyContent(FlexAlign.Center)  
  
      Row(){  
        Text("出发时间")  
        Text(`2024-7-9 ${this.hour}:${this.minute}:00`)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Text("12小时制")  
  
        TimePicker({selected:this.date}).width(200)  
          .onChange((date) => {  
            this.date = new Date(`2024-7-9 ${date.hour}:${date.minute}:00`)  
          })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Text("24小时制")  
  
        TimePicker({selected:new Date(`2024-7-9 ${this.hour}:${this.minute}`)}).width(200)  
          .onChange((date) => {  
            this.hour = date.hour  
            this.minute = date.minute  
          }).useMilitaryTime(true)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
    }.width("100%").height("100%")  
  }  
}
```

TimePicker時間選擇器，接受一個選項，與文本選擇器不同，一般直接確定的時間，可以在範圍内取值

```typescript
const TimePicker: TimePickerInterface
(options?: TimePickerOptions) => TimePickerAttribute
```

内容更新時觸發onChange

可通過useMilitaryTime修改顯示時間格式

#### 日期選擇器

```typescript
@Component  
export default struct Test8{  
  @State date:DatePickerResult = {}  
  @State selectedDate:string = ""  
  
  aboutToAppear(){  
    this.selectedDate = "2024-7-9 00:00:00"  
    var now = new Date(this.selectedDate)  
    this.date.year = now.getFullYear()  
    this.date.month = now.getMonth()  
    this.date.day = now.getDay()  
  }  
  
  build(){  
    Column(){  
      Row(){  
        Text("日期选择器").fontColor("#FFF").fontWeight(700).fontSize(20)  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center)  
      .backgroundColor("#ff106922")  
  
      Text(`${this.date.year}-${this.date.month}-${this.date.day} 00:00:00`)  
      Text(new Date(`${this.date.year}-${this.date.month}-${this.date.day} 00:00:00`).toString())  
  
      Row(){  
        Text("出生年月")  
        DatePicker({selected:new Date(this.selectedDate)})  
          .width(200).onChange((date) => {  
          this.date.year = date.year  
          this.date.month = date.month + 1  
          this.date.day = date.day  
          this.selectedDate = `${date.year}-${date.month + 1}-${date.day} 00:00:00`          console.log(`date year: ${date.year}`)  
        })  
      }.width("100%").justifyContent(FlexAlign.SpaceBetween).padding({ left:10,right:10 })  
      .backgroundColor("#ff989898")  
  
      Row(){  
        Text("出生年月农历")  
        DatePicker({selected:new Date(this.selectedDate)})  
          .width(200).onChange((date) => {  
          this.date.year = date.year  
          this.date.month = date.month + 1  
          this.date.day = date.day  
          this.selectedDate = `${date.year}-${date.month + 1}-${date.day} 00:00:00`        }).lunar(true)  
      }.width("100%").justifyContent(FlexAlign.SpaceBetween).padding({ left:10,right:10 })  
      .backgroundColor("#ff989898")  
  
    }.width("100%").height("100%")  
  }  
}
```

DatePicker日期選擇器，接受一個選項，常見默認選擇日期

```typescript
const DatePicker: DatePickerInterface
(options?: DatePickerOptions) => DatePickerAttribute
```

可通過lunar指定顯示模式為陰曆，此時内部表示日期的值依然是陽曆

#### 單選框

```typescript
@Component  
export default struct Test9{  
  @State roleArray:Array<string> = ["管理员","研发人员","资料人员","测试人员","考勤员"]  
  @State roleSelect:Array<boolean> = []  
  @State selectIndex:number = 2  
  
  aboutAppear(){  
    this.roleArray.forEach(() => {  
      this.roleSelect.push(false)  
    })  
    this.roleSelect[this.selectIndex] = true  
  }  
  
  build(){  
    Column(){  
      Row(){  
        Text("角色列表").fontColor("#FFF")  
      }.backgroundColor("#f200").width("100%").justifyContent(FlexAlign.Center).padding(10)  
  
      Column(){  
        ForEach(this.roleArray, (item, index) =>{  
          Row(){  
            Radio({group:"roleManage1",value:item}).margin({right:20})  
              .checked(this.roleSelect[index])  
              .onChange((val) => {  
                this.roleSelect[index] = val  
                if(val){  
                  this.selectIndex = index  
                }  
              })  
            Text(item)  
          }.width(100)  
        })  
      }.height(200).justifyContent(FlexAlign.SpaceEvenly)  
  
      Column(){  
        ForEach(this.roleArray, (item, index) =>{  
          Row(){  
            Radio({group:"roleManage2",value:item}).margin({right:20})  
              .checked(this.roleSelect[index])  
              .onChange((val) => {  
                this.roleSelect[index] = val  
                if(val){  
                  this.selectIndex = index  
                }  
              })  
            Text(item)  
          }.width(100)  
        })  
      }.height(200).justifyContent(FlexAlign.SpaceEvenly)  
  
      Row(){  
        Text("选中角色")  
        Text(this.roleArray[this.selectIndex])  
      }.width("100%").justifyContent(FlexAlign.SpaceBetween).padding(10)  
    }.width("100%").height("100%")  
  }  
}
```

Radio為單選框，接受一個選項，不僅需要設定值，還要劃分組別

```typescript
const Radio: RadioInterface
(options: RadioOptions) => RadioAttribute
```

checked在被選中時執行，onChange在選擇狀態改變時執行
