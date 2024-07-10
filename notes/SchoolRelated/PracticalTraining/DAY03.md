#  暑期實訓 其之三

## 前言

接[上文](./DAY02.html)

此頁記錄實訓第三天内容，今日全程熟悉組件用法

首先給出結論，前三天的所有内容不出一個上午就可以全部掌握，之後應該也會繼續花幾天時間消耗在試用功能這種瑣事上，所以“名稱-使用示例”這樣的呈現方式可能還得持續些篇幅

某人認爲更合適的學習路綫

1. 基礎用法和概念直接查看[文檔](https://developer.huawei.com.cn/consumer/cn/doc/)

2. 詳細信息去翻[項目](https://gitee.com/openharmony/docs)

另外，雖説鴻蒙開發和安卓開發名義上劃清界限了，但是按照目前看來至少界面的設計還是很有相通之處的。再加上縫合了多種現代語言的特點，所以對於有開發基礎的人來説，上手只會更快

## 主要内容

#### 複選框組件

```typescript
@Component  
export default struct Test10{  
  @State  
  studentArray:Array<string> = ["小学","中学","高中","一本","二本","三本","研究生"]  
  
  @State  
  @Watch("countStudentName")  
  checkArray:Array<boolean> = []  
  
  @State  
  selectIndex1:Array<number> = [2,3]  
  
  @State  
  selectIndex:Array<number> = []  
  
  @State  
  checkStudentName:string = ""  
  
  
  countStudentName(){  
    this.checkStudentName = ""  
    this.selectIndex.splice(0, this.selectIndex.length)  
    this.checkArray.forEach((item, index) => {  
      if(item){  
        this.checkStudentName += this.studentArray[index] + "|"  
        this.selectIndex.push(index)  
      }  
    })  
  }  
  
  aboutToAppear(){  
    this.studentArray.forEach(() => {  
      this.checkArray.push(false)  
    })  
  
    this.selectIndex1.forEach((item) =>{  
      this.checkArray[item] = true  
    })  
  }  
  
  build(){  
    Column() {  
      Row() {  
        Text("学历管理").fontColor("#FFF").fontSize(20).fontWeight(700)  
      }.backgroundColor("#FF0").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Column(){  
        ForEach(this.studentArray, (item, index) => {  
          Row() {  
            Checkbox({ group: "student", name: item })  
              .margin({ right: 40 }).select(this.checkArray[index])  
              .onChange((val) => {  
                this.checkArray[index] = val  
              })  
            Text(item)  
          }.width(100)  
        })  
      }.height(200).justifyContent(FlexAlign.SpaceEvenly)  
  
      Divider()  
  
      Column(){  
        ForEach(this.studentArray, (item, index) => {  
          Row() {  
            Checkbox({ group: "student", name: item })  
              .margin({ right: 40 }).select(this.checkArray[index])  
              .onChange((val) => {  
                this.checkArray[index] = val  
              })  
            Text(item)  
          }.width(100)  
        })  
      }.height(200).justifyContent(FlexAlign.SpaceEvenly)  
  
      Divider()  
  
      Row() {  
        Text("学历")  
        Text(this.checkStudentName)  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Row() {  
        Text("索引")  
        Text(JSON.stringify(this.selectIndex))  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
    }.width("100%").height("100%")  
  }  
}
```

#### 下拉框組件

```typescript
@Component  
export default struct Test11{  
  @State  
  sexArray:Array<SelectOption> = [  
    {value:"请选择"},  
    {value:"男"},  
    {value:"女"}  
  ]  
  
  @State  
  sexValue:string = "请选择"  
  
  @State  
  runArray:Array<SelectOption> = [  
    {value:"请选择"},  
    {value:"足球"},  
    {value:"篮球"},  
    {value:"网球"},  
    {value:"排球"}  
  ]  
  
  @State  
  runIndex:number = 1  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("下拉列表")  
      }.backgroundColor("#f466").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Row(){  
        Text("性别")  
        Select(this.sexArray).value(this.sexValue)  
          .onSelect((index,item) => {  
            this.sexValue = item  
          })  
  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Text("性别值")  
        Text(this.sexValue)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("运动")  
        Select(this.runArray).selected(this.runIndex)  
          .value(this.runArray[this.runIndex].value.toString())  
          .onSelect((index) => {  
            this.runIndex = index  
          })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("运动索引")  
        Text(this.runIndex + "")  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
    }.width("100%").height("100%")  
  }  
}
```

#### 滑動組件

```typescript
@Component  
export default struct Test12{  
  
  @State  
  startFlag:boolean = true  
  
  @State  
  sendFlag:boolean = false  
  
  @State  
  stopFlag:boolean = false  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("滑动组件").fontColor("#FFF").padding(20)  
      }.backgroundColor("#F330").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Row(){  
        Text("是否启动")  
        Toggle({type:ToggleType.Switch,isOn:this.startFlag}).onChange((val) => {  
          this.startFlag = val  
        })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("是否启动")  
        Toggle({type:ToggleType.Switch,isOn:this.startFlag}).onChange((val) => {  
          this.startFlag = val  
        })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("是否启动")  
        Text(this.startFlag + "")  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("发送消息")  
        Toggle({type:ToggleType.Checkbox,isOn:this.sendFlag}).onChange((val) => {  
          this.sendFlag = val  
        })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("发送消息")  
        Text(this.sendFlag + "")  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
  
      Row(){  
        Text("停止运行")  
        Toggle({type:ToggleType.Button,isOn:this.stopFlag}).onChange((val) => {  
          this.stopFlag = val  
        }).width(70)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("停止运行")  
        Toggle({type:ToggleType.Button,isOn:this.stopFlag}).onChange((val) => {  
          this.stopFlag = val  
        }).width(70).backgroundColor("#AAA").selectedColor("#0F0")  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
  
      Row(){  
        Text("停止运行")  
        Text(this.stopFlag + "")  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
    }.width("100%").height("100%")  
  }  
}
```

#### 進度條組件

```typescript
@Component  
export default struct Test13{  
  
  @State  
  value:number = 0  
  
  @State  
  total:number = 250  
  
  addProgress(){  
    setTimeout(() => {  
      this.addProgress()  
      this.value += 10  
      if(this.value > this.total + 20){  
        this.value = 0  
      }  
    }, 1000)  
  }  
  
  aboutToAppear(){  
    this.addProgress()  
  }  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("进度条").fontSize(20).fontColor("#FFF")  
      }.backgroundColor("#FF0").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Scroll(){  
        Column(){  
          Row(){  
            Text("进度")  
            Text(this.value + "")  
          }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
          Progress({value:this.value, total:this.total})  
  
          Progress({value:this.value, total:this.total, type:ProgressType.Ring}).width(200)  
            .rotate({angle:180}).color("#F00").backgroundColor("#00F")  
  
          Progress({value:this.value, total:this.total, type:ProgressType.ScaleRing}).width(200)  
            .color("#FF0").backgroundColor("#F00")  
  
          Progress({value:this.value, total:this.total, type:ProgressType.Eclipse}).width(200)  
  
          Progress({value:this.value, total:this.total, type:ProgressType.Capsule}).width(200)  
  
        }.justifyContent(FlexAlign.SpaceEvenly)  
      }.flexGrow(1)  
    }.width("100%").height("100%")  
  }  
}
```

#### 搜索組件

```typescript  
@Component  
export default struct Test14{  
  
  @State  
  searchVal:string = ""  
  
  @State  
  srcArray:Array<string> = [****]  
  
  @State  
  descArray:Array<string> = []  
  
  @State  
  scrollHeight:number = 0  
  
  scroll:Scroller = new Scroller()  
  
  aboutToAppear(){  
    this.descArray = [...this.srcArray]  
    this.scrollHeight = this.descArray.length * 40  
  }  
  
  build() {  
    Column(){  
      Row(){  
        Text("搜索组件").fontColor("#FFF").fontSize(20)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween).backgroundColor("#F511")  
  
      Search({placeholder:"请输入条件", value:this.searchVal}).onSubmit((val) => {  
        this.searchVal = val  
        this.descArray.splice(0,this.descArray.length)  
        if(val === ""){  
          this.descArray = [...this.srcArray]  
        }  
        else{  
          this.srcArray.forEach((item) => {  
            if(item.indexOf(val) !== -1){  
              this.descArray.push(item)  
            }  
          })  
        }  
        this.scroll.scrollEdge(Edge.Top)  
        this.scrollHeight = this.descArray.length * 40  
      })  
  
      Scroll(this.scroll){  
        Column(){  
          ForEach(this.descArray, (item,index) => {  
            Row(){  
              Text(index + "")  
              Text(item)  
            }.width("100%").height(40).justifyContent(FlexAlign.SpaceBetween)  
          })  
        }  
      }.flexGrow(1).backgroundColor("#F00").height(this.scrollHeight)  
  
    }.width("100%")  
  }  
}
```

這裏某人畫蛇添足了許多内容，起因是示例中發生了一個小問題，情況復現如下：

1. 未進行搜索時，顯示所有記錄，由於記錄較多，所以很自然地充斥整個界面
2. 執行搜索，示例中的邏輯為根據輸入關鍵詞進行局部匹配
3. 儅篩選結束時，若匹配項較少，則這些項目會在豎直方向上居中顯示

根據講師對多個區域著色嘗試定位每個組件區域推測，應該不是示例原意，但是講師嘗試未果后放棄更正

某人排查后發現，是Scroll的父元素Column的height屬性被設爲100%導致高度撐開了整個屏幕，儅内部元素高度不夠時，被自動置中了，因此去掉該屬性賦值即可

期間嘗試使用option來創建scroll，在搜索執行結束后對Scroller類對象執行scrollEdge方法使其滾動到邊界，然而這對於自身高度小於父組件高度的scroll來説是無效的，因爲顯示時已然為滾動至邊界狀態

此外還嘗試了固定元素的外觀如高度之類的屬性，雖然也沒有效果，但是期間某人才意識到是父元素的問題

該問題的排查暴露了某人在前端基礎上的不足，若是熟知常見的佈局模型，應該能少走彎路

#### 面板組件

```typescript  
@Component  
export default struct Test15{  
  
  @State  
  show:boolean = true  
  
  @State  
  birthday:DatePickerResult = {year:1980, month:1, day:4}  
  
  birthdayValue:string = ""  
  
  aboutToAppear(){  
    this.birthdayValue = `${this.birthday.year}-${this.birthday.month}-${this.birthday.day}`  
  }  
  
  build(){  
    Column(){  
      Row(){  
        Text("面板组件").fontColor("#FFF")  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center).backgroundColor("#F800")  
  
      Row(){  
        Text("出生年月")  
        Text(`${this.birthday.year}-${this.birthday.month}-${this.birthday.day}`)  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center)  
      .onClick(() => {  
        this.show = !this.show  
      })  
  
      Divider()  
  
      Panel(this.show){  
        Row(){  
          Text("编译出生日期")  
        }.padding(10)  
  
        Divider()  
  
        DatePicker({selected:new Date(`${this.birthdayValue} 01:01:01`)}).width(200).onChange((date)=>{  
          this.birthday.year = date.year  
          this.birthday.month = date.month + 1  
          this.birthday.day = date.day  
          this.birthdayValue = `${this.birthday.year}-${this.birthday.month}-${this.birthday.day}`  
          console.info("date year:" + JSON.stringify(date))  
        })  
  
        Button("确认").width("95%").margin(10).onClick(() => {  
          this.show = false  
        })  
      }.miniHeight(100).mode(PanelMode.Mini)  
  
    }.width("100%").height("100%")  
  }  
}
```

此處用到了DatePicker，和前一次使用時一樣，示例中將birthday同時作爲被修改值用來記錄當前選定值，又作爲選擇器打開時的選擇初值，這在使用IDE提供的模擬器運行時會出現選擇值跳動問題，並無法滾動到指定的内容

解決思路也是一樣的，既然selected屬性值只在一開始作用，而變值只在onChange方法中進行改變，那麽完全可以替換掉selected中對birthday的引用。option的具體生效方式可以去項目倉庫中搜索