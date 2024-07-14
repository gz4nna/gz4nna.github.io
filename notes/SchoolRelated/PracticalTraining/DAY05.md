#  暑期實訓 其之五

## 前言

接[上文](./DAY04.html)

此頁記錄實訓第五天内容，這一次反而又開始試用控件了

大體流程和前些天一樣，給出示例后復現，然後下一個控件，沒什麽值得記錄的地方

## 主要内容

#### 路由传参

```typescript
import router from '@ohos.router'  
@Component  
export default struct Test22{  
  
  @State  
  userName:string = ""  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("路由传参").fontColor("#fff").fontSize(20)  
      }.backgroundColor("#f065").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Divider()  
  
      Row(){  
        Text("参数")  
        TextInput({placeholder:"请输入参数", text:this.userName}).onChange((val) => {  
          this.userName = val  
        })  
      }  
  
      Divider()  
  
      Button("提交").onClick(() => {  
        var reg = new RegExp("^.+$")  
        if(this.userName == null || this.userName == "" || !reg.test(this.userName)){  
          AlertDialog.show({message:"请输入用户名称"})  
          return  
        }  
        router.pushUrl({url:"pages/ParamerPage",params:{"userName":this.userName}})  
      })  
  
      Button("提交1").onClick(() => {  
        router.pushUrl({url:"pages/ParamerPage"})  
      })  
    }  
  }}
```

在DAY04中曾經嘗試過了直接使用的路由跳轉功能，但是在實際使用中，跳轉前後的兩個頁面基本上都是需要有數據交換的

此時可以通過在RouterOptions裏添加所傳參數

```typescript
interface RouterOptions {
	url: string;
	params?: Object
}
```

作爲接受跳轉的頁面

```typescript
import router from '@ohos.router'  
@Entry  
@Component  
struct ParamerPage {  
  @State  
  message: string = '接收参数'  
  
  @State  
  userName:string = ""  
  
  aboutToAppear(){  
    var param = router.getParams()["userName"]  
    if(param){  
      this.userName = router.getParams()["userName"]  
    }  
  }  
  build() {  
    Column(){  
      Row(){  
  
        Row(){  
          Text("接收参数").fontSize(20).fontColor("#fff")  
            .onClick(() => {  
              router.back()  
            })  
        }.flexGrow(1).justifyContent(FlexAlign.Center)  
  
      }.backgroundColor("#f170").width("100%").padding(10)  
  
      Row(){  
        Text("用户名")  
        Text(this.userName)  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Divider()  
    }.width("100%").height("100%")  
  }  
}
```

可以通過 router.getParams()\[參數鍵名] 來獲取傳入的内容

#### 轮播组件

```typescript
import router from '@ohos.router'  
  
@Component  
export default struct Test23{  
  build(){  
    Stack({alignContent:Alignment.BottomEnd}){  
      Swiper(){  
        Text("1")  
        Text("2")  
        Text("3")  
        Text("4")  
        Text("5")  
      }.indicatorStyle({size:25, color:"#f00", selectedColor:"#0f0"})  
      .autoPlay(true).onAnimationEnd((index) => {  
        if(index == 3){  
          router.replaceUrl({url: "pages/RolePage"})  
        }  
      })  
  
      Text("跳转应用").fontColor("#0f0").padding(10).fontSize(20)  
        .onClick(() => {  
          router.replaceUrl({url: "pages/RolePage"})  
        })  
    }.width("100%").height("100%")  
  }  
}
```

Swiper 可以輪播其中元素

通過 indicatorStyle 可以指定輪播時底部指示條的樣式，onAnimationEnd 可以指定在一次切換動畫結束時執行的動作

```typescript
@Component  
export default struct Test24{  
  
  control:SwiperController = new SwiperController()  
  
  
  build(){  
    Column(){  
      Stack(){  
        Swiper(this.control){  
          Text("1")  
          Text("2")  
          Text("3")  
          Text("4")  
          Text("5")  
        }.indicatorStyle({size:25, color:"#f00", selectedColor:"#0f0"})  
        .autoPlay(true)  
  
        Row(){  
          Text("上一个").fontColor("#0f0").fontWeight(800)  
            .onClick(() => {  
              this.control.showPrevious()  
            })  
          Text("下一个").fontColor("#0f0").fontWeight(800)  
            .onClick(() => {  
              this.control.showNext()  
            })  
        }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      }.backgroundColor("#ccc")  
    }.width("100%").height("100%").justifyContent(FlexAlign.Center)  
  }  
}
```

#### tabs组件

```typescript
@Component  
export default struct Test25{  
  
  @State  
  selectIndex:number = 0  
  
  control:TabsController = new TabsController()  
  
  @Builder  
  tarBar(text:string, index:number){  
    Column(){  
      if(this.selectIndex === index) {  
        Text(text).fontSize(20).fontColor("#f00").fontWeight(600)  
      }  
      else{  
        Text(text).fontSize(20).fontWeight(600)  
      }  
    }.onClick(() => {  
      this.control.changeIndex(index)  
    })  
  }  
  
  
  build(){  
    Tabs({barPosition:BarPosition.End, index:this.selectIndex}) {  
  
      TabContent() {  
        Text("主页").fontSize(40)  
      }.tabBar(this.tarBar("主页", 0))  
  
      TabContent() {  
        Text("查找").fontSize(40)  
      }.tabBar(this.tarBar("查找", 1))  
  
      TabContent() {  
        Text("通讯录").fontSize(40)  
      }.tabBar(this.tarBar("通讯录", 2))  
  
      TabContent() {  
        Text("我的").fontSize(40)  
      }.tabBar(this.tarBar("我的", 3))  
  
    }.onChange((index) => {  
      this.selectIndex = index  
    })  
  }  
}
```

tabs 控件常常用作單個頁面中的内容切換，也可以結合路由實現導航的功能

#### 走马灯组件

```typescript
const contents:string = "阿斯顿法国红酒看来去微软推哦怕自行车v不那么去啊自我实现二的吃软饭v提供必要和奴家拍咯咯密集怒吼吧应该v她非常弱的学而思欺骗我欸如天涯螺丝扣搭街坊换个名字那些"  
  
@Component  
export default struct Test26{  
  
  @State  
  start:boolean = true  
  
  @State  
  from:boolean = true  
  
  @State  
  step:number = 6  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("走马灯").fontColor("#fff").fontSize(20)  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center).backgroundColor("#f055")  
  
      Column(){  
        Marquee({start:this.start,src:contents,fromStart:this.from,step:this.step})  
  
        Row(){  
          Button("开始").onClick(() => {  
            this.start = true  
          })  
  
          Button("停止").onClick(() => {  
            this.start = false  
          })  
        }.justifyContent(FlexAlign.SpaceBetween).width("100%")  
  
        Row(){  
          Button("正向").onClick(() => {  
            this.from = true  
          })  
  
          Button("反向").onClick(() => {  
            this.from = false  
          })  
        }.justifyContent(FlexAlign.SpaceBetween).width("100%")  
  
        Row(){  
          Button("加速").onClick(() => {  
            this.step += 6  
          })  
  
          Button("减速").onClick(() => {  
            this.step -= 6  
          })  
        }.justifyContent(FlexAlign.SpaceBetween).width("100%")  
  
      }.flexGrow(1).justifyContent(FlexAlign.SpaceAround)  
  
    }.width("100%").height("100%")  
  }  
}
```

#### 屏幕监听

```typescript
import mediaquery from '@ohos.mediaquery'  
@Component  
export default struct Test27{  
  
  @State  
  horizontalFlag:boolean = true  
  
  listener:mediaquery.MediaQueryListener = mediaquery.matchMediaSync("(orientation:landscape)")  
  
  aboutToAppear(){  
    this.listener.on("change",(result:mediaquery.MediaQueryResult) => {  
      this.horizontalFlag = result.matches  
    })  
  }  
  
  
  build(){  
    if(this.horizontalFlag){  
      Row(){  
        Column(){  
          Text("横屏").fontSize(30)  
        }.flexBasis(50).padding(10)  
        .border({width:{right:1}}).height("100%").justifyContent(FlexAlign.Center)  
  
        Column(){  
          Text("通过不会被大风吹v哦空么大出风头应该会")  
        }.flexGrow(1).justifyContent(FlexAlign.Center)  
  
      }.width("100%").height("100%")  
    }  
    else{  
      Column(){  
        Row(){  
          Text("竖屏").fontSize(30)  
        }.width("100%").padding(10).border({width:{bottom:1}}).justifyContent(FlexAlign.Center)  
  
        Column(){  
          Text("去啊是的乳房v关于何炅i看楼主下次发给")  
        }.flexGrow(1).justifyContent(FlexAlign.Center)  
  
      }.width("100%").height("100%")  
    }   
  }
}
```

名稱為屏幕監聽，實際上示例中只給出了設備橫竪屏狀態監聽，有關監聽器的功能應該還有其他的，待查看文檔后補充

#### 对话框组件

```typescript
import promptAction from '@ohos.promptAction'  
  
const button_confirm:number = 0  
const button_cancel:number = 1  
  
@Component  
export default struct Test28{  
  
  @State  
  message:string = ""  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("弹出框").fontSize(20).fontColor("#fff")  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center).backgroundColor("#f274")  
  
      Column(){  
        Button("弹出框").onClick(() => {  
          promptAction.showToast({message:"好好学习"})  
        })  
  
        Button("弹出框1").onClick(() => {  
          promptAction.showToast({message:"好好学习1", duration:2000, bottom:400})  
        })  
  
        Button("对话框").onClick(() => {  
          promptAction.showDialog({message:"登陆成功"})  
        })  
  
        Text("点击了" + this.message)  
  
        Button("对话框1").onClick(() => {  
          promptAction.showDialog({message:"登陆成功1",buttons:[  
            {text:"确认",color:"#00f"},  
            {text:"取消",color:"#00f"},  
            {text:"复制",color:"#00f"}  
          ]}).then((event) => {  
            console.info("prompAction button:click" + JSON.stringify(event))  
            if(event.index === button_confirm){  
              this.message = "对话框-确认按钮"  
            }  
            else if(event.index === button_cancel){  
              this.message = "对话框-取消按钮"  
            }  
          })  
        })  
  
        Button("alert对话框").onClick(() => {  
          AlertDialog.show({message:"登陆失败"})  
        })  
  
        Button("alert对话框1").onClick(() => {  
          AlertDialog.show({message:"认证成功",confirm:{  
            value:"确认",action:() => {  
              this.message = "alert对话框-确认"  
            }  
          }})  
        })  
  
        Button("alert对话框1").onClick(() => {  
          AlertDialog.show({  
            message:"认证成功",  
            primaryButton:{  
              value:"确认",  
              action:() => {  
                this.message = "alert对话框-primaryButton"  
              }  
            },  
            secondaryButton:{  
              value:"取消",  
              action:() => {  
                this.message = "alert对话框-secondaryButton"  
              }  
            }          })  
        })  
  
  
      }.flexGrow(1).justifyContent(FlexAlign.SpaceEvenly)  
    }.width("100%").height("100%")  
  }  
}
```
