#  暑期實訓 其之六

## 前言

接[上文](./DAY05.html)

第六天

## 主要内容

#### 自定義對話框

```typescript
@CustomDialog  
struct DialogUi{  
  controller:CustomDialogController  
  build(){  
    Column(){  
      Row(){  
        Text("角色分配")  
      }  
  
      Divider().margin(10)  
  
      Column(){  
        Row(){  
          Checkbox()  
          Text("管理员")  
        }.width(150)  
  
        Row(){  
          Checkbox()  
          Text("开发人员")  
        }.width(150)  
  
        Row(){  
          Checkbox()  
          Text("调试人员")  
        }.width(150)  
      }  
  
      Divider().margin(10)  
  
      Row(){  
        Button("确定").onClick(() => {  
          AlertDialog.show({message:"登录成功"})  
        })  
        Button("取消").onClick(() => {  
          this.controller.close()  
        })  
      }.width(200).justifyContent(FlexAlign.SpaceBetween)  
    }.padding(10)  
  }  
}  
  
@Component  
export default struct Test29{  
  
  controller:CustomDialogController = new CustomDialogController({  
    builder:DialogUi()  
  })  
  build(){  
    Button("打开对话框").onClick(() => {  
      this.controller.open()  
    })  
  }  
}
```

#### 屬性動畫

```typescript
@Component  
export default struct Test30{  
  
  @State  
  cWidth:number = 80  
  
  @State  
  angle:number = 0  
  
  build(){  
    Column(){  
      Row(){  
        Text("属性动画").fontColor("#fff").fontSize(20)  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center).backgroundColor("#f671")  
  
      Column(){  
  
        Row().width(this.cWidth).height(this.cWidth).backgroundColor("#f00").margin(20)  
          .borderRadius(this.cWidth).onClick(() => {  
          if(this.cWidth === 80){  
            this.cWidth *= 4  
          }  
          else{  
            this.cWidth = 80  
          }  
        }).animation({  
          duration:2000,  
          playMode:PlayMode.Alternate,  
          curve:Curve.EaseInOut,  
          iterations:-1  
        })  
  
        Row().width(100).height(10).backgroundColor("#f0f").rotate({angle:this.angle})  
          .onClick(() => {  
            this.angle += 180  
          })  
          .animation({  
            duration:2000,  
            playMode:PlayMode.Normal,  
            curve:Curve.EaseInOut,  
            iterations:-1  
          })  
  
        Row().width(100).height(10).backgroundColor("#f182").rotate({angle:this.angle,y:200})  
          .onClick(() => {  
            this.angle += 180  
          })  
          .animation({  
            duration:2000,  
            playMode:PlayMode.Normal,  
            curve:Curve.EaseInOut,  
            iterations:-1  
          })  
  
        Row().width(100).height(10).backgroundColor("#fba1").rotate({angle:this.angle,x:200})  
          .onClick(() => {  
            this.angle += 180  
          })  
          .animation({  
            duration:2000,  
            playMode:PlayMode.Normal,  
            curve:Curve.EaseInOut,  
            iterations:-1  
          })  
  
      }.flexGrow(1).justifyContent(FlexAlign.SpaceEvenly)  
  
    }.width("100%").height("100%")  
  }  
}
```

#### 顯示動畫

```typescript
@Component  
export default struct Test31{  
  @State diceImage:Resource = $r("app.media.app_icon")  
  @State imageArray:Array<Resource> = [$r("app.media.app_icon"),$r("app.media.app_icon"),$r("app.media.app_icon"),$r("app.media.app_icon")]  
  @State angle:number = 0  
  @State baArray:Array<Resource> = [$r("app.media.app_icon"),$r("app.media.app_icon"),$r("app.media.app_icon"),$r("app.media.app_icon")]  
  @State baImage:Resource =$r("app.media.app_icon")  
  @State baIndex:number = 0  
  @State marginRight:number=0  
  random(){  
    setTimeout(()=>{  
      this.random()  
      var random = Math.random()*6  
      var index = parseInt(random.toString())  
      animateTo({playMode:PlayMode.Normal,  
        iterations:index+1,  
        curve:Curve.EaseInOut,  
        duration:1000},()=>{  
        this.diceImage = this.imageArray[index]  
        this.marginRight += 100  
      })  
    },1000)  
  }  
  ba(){  
    setTimeout(()=>{  
      this.ba()  
      this.baIndex++  
      if (this.baIndex>8) {  
        this.baIndex = 0  
      }  
      animateTo({  
        playMode:PlayMode.Normal,  
        iterations:this.baIndex+1,  
        curve:Curve.EaseInOut,  
        duration:500  
      },()=>{  
        this.baImage=this.baArray[this.baIndex]  
        this.marginRight += 5  
        if (this.marginRight>200) {  
          this.marginRight = 0  
        }  
      })  
    },500)  
  }  
  build(){  
    Column(){  
      Row(){  
        Text("显示动画").fontSize(20).fontColor("#FFF")  
      }.width("100%").padding(10).justifyContent(FlexAlign.Center)  
      .backgroundColor("#ff173c86")  
      Row(){  
        Image(this.diceImage).width(50).rotate({angle:this.angle,y:200})  
      }.flexBasis(100).width("100%").padding(10).justifyContent(FlexAlign.Center)  
      Row(){  
        Button("摇骰子").onClick(()=>{  
          this.random()  
        })  
      }  
      Row(){  
        Image(this.baImage).width(150).margin({right:this.marginRight})  
      }.flexBasis(200).width("100%").padding(10).justifyContent(FlexAlign.End)  
      .backgroundColor("#f00")  
      Row(){  
        Button("孙悟空").onClick(()=>{  
          this.ba()  
        })  
      }  
    }.width("100%").height("100%")  
  }  
}
```

#### 轉場動畫

```typescript
import router from '@ohos.router'  
@Component  
export default struct Test32{  
  @State myOpacity:number = 1  
  
  pageTransition(){  
    PageTransitionEnter({  
      duration:500,  
      curve:Curve.EaseIn  
    }).onEnter((type: RouteType, progress: number) => {  
      this.myOpacity = progress  
    })  
    PageTransitionExit({  
      duration:500,  
      curve:Curve.EaseOut  
    }).onExit((type: RouteType, progress: number) => {  
      this.myOpacity = 1 - progress  
    })  
  }  
  
  build(){  
    Column(){  
      Row(){  
        Text("转场动画").fontColor("#FFF").fontSize(20).fontWeight(700)  
      }.backgroundColor("#ff257e38").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Column(){  
        Button("汽车之家").onClick(()=>{  
          router.pushUrl({url:"pages/CarHomePage"})  
        })  
  
        Button("教师之家").onClick(()=>{  
          router.pushUrl({url:"pages/TeacherHomePage"})  
        })  
      }.flexGrow(1).justifyContent(FlexAlign.SpaceEvenly).opacity(this.myOpacity)  
    }.width("100%").height("100%")  
  }  
}
```

#### 插槽注解

```typescript
import StudentInfo from "../models/StudentInfo"  
@Component  
struct ListInfo{  
  @Link list:Array<StudentInfo>  
  @BuilderParam title:()=>void  
  @BuilderParam showListItem:(data:StudentInfo,index?:number)=>void  
  build(){  
    Column() {  
      Row(){  
        this.title()  
      }.width("100%").padding(10)  
      List() {  
        ForEach(this.list, (item: StudentInfo, index) => {  
          ListItem() {  
            this.showListItem(item,index)  
          }  
        })  
      }  
    }  }}  
  
@Component  
export default struct Test33{  
  @State  
  list:Array<StudentInfo> = [  
    {name:"张三",mobile:"123564678"},  
    {name:"李四",mobile:"156786464"},  
    {name:"王五",mobile:"194564634"},  
  ]  
  
  @Builder  
  titleShow(){  
    Row(){  
      Text("标题").fontColor("#FFF").fontSize(20).fontWeight(700)  
    }.backgroundColor("#ff4def12").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  }  
  
  @Builder  
  LItemShow(item:StudentInfo,index:number){  
    if(index % 2 === 0){  
      Row(){  
        Text(index.toString())  
        Text(item.name).fontSize(20)  
        Text(item.mobile).fontSize(20)  
      }.width("100%").justifyContent(FlexAlign.SpaceBetween).backgroundColor("#EEE").padding(10)  
    }else{  
      Row(){  
        Text(index.toString())  
        Text(item.name).fontSize(20)  
        Text(item.mobile).fontSize(20)  
      }.width("100%").justifyContent(FlexAlign.SpaceBetween).backgroundColor("#EE0").padding(10)  
    }  
  }  
  
  build(){  
    Column(){  
      Row(){  
        Text("插槽注解").fontColor("#FFF").fontSize(20).fontWeight(700)  
      }.backgroundColor("#ff12deef").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      ListInfo({list:$list,title:this.titleShow,showListItem:this.LItemShow})  
    }.width("100%").height("100%")  
  }  
}
```

#### 插槽對話框

```typescript
@CustomDialog  
struct DialogUI{  
  @Prop title:string  
  @BuilderParam body:()=>void  
  confirmAction:()=>void  
  dialogControl:CustomDialogController  
  build(){  
    Column(){  
      Row(){  
        Text(this.title).fontSize(20).fontColor("#FFF")  
      }.backgroundColor("#fff30dba").width("100%").padding(10).justifyContent(FlexAlign.Center)  
      this.body()  
      Row(){  
        Button("确认").onClick(()=>{  
          this.confirmAction()  
        })  
        Button("取消").onClick(()=>{  
          this.dialogControl.close()  
        })  
      }.width(200).padding(10).justifyContent(FlexAlign.SpaceBetween)  
    }.padding(10)  
  }  
}  
  
@Component  
export default struct Test34{  
  dialogControl:CustomDialogController = new CustomDialogController({  
    builder:DialogUI({  
      title:"分配角色",  
      body:this.dialogBody,  
      confirmAction:()=>{  
        AlertDialog.show({message:"分配角色失败"})  
      }  
    })  
  })  
  
  @Builder  
  dialogBody(){  
    Column(){  
      Row(){  
        Checkbox()  
        Text("用户管理")  
      }  
  
      Row(){  
        Checkbox()  
        Text("角色管理")  
      }  
  
      Row(){  
        Checkbox()  
        Text("配置管理")  
      }  
    }  }  
  build(){  
    Column(){  
      Row(){  
        Text("尾随闭合函数").fontColor("#FFF").fontSize(20).fontWeight(700)  
      }.backgroundColor("#fff30dba").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Column(){  
        Button("打开").onClick(()=>{  
          this.dialogControl.open()  
        })  
      }.flexGrow(1)  
    }.width("100%").height("100%")  
  }  
}
```