#  暑期實訓 其之四

## 前言

接[上文](DAY03.html)

此頁記錄實訓第四天内容，除了和先前一樣的熟悉組件用法之外，還涉及了一些佈局相關的基礎

此外，講師提及接下來即將安排進行網絡授課學習，準備考取某認證

## 主要内容

首先延續先前内容，介紹了最後兩個常見的功能性控件元素的使用方法：列表組件和列表分組組件
### 功能性控件

#### 列表組件

```typescript
@Component  
export default struct Test16{  
  
  @State  
  studentArray:Array<string> = [****]  
  
  @Builder  
  editRowInfo(item:string, index:number){  
    Row(){  
      Search({value:item, icon:""}).onSubmit((val) => {  
        this.studentArray[index] = val  
      })  
    }.padding(10).height(50)  
  
  }  
  
  build(){  
    Column(){  
      Row(){  
        Text("人员列表").fontColor("#FFF")  
  
      }.backgroundColor("#F283").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      List(){  
        ForEach(this.studentArray, (item, index) => {  
          ListItem(){  
            Row(){  
              Text(index.toString())  
              Text(item)  
            }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
            .border({width:{bottom:1},color:"#BBB"}).height(50)  
          }.swipeAction({end:this.editRowInfo(item,index)})  
        })  
      }.height("95%")  
    }  
  }
}
```

其中，由於在搜索框内引用了網絡資源，所以需要將應用的網絡訪問權限打開

在 module.json5 文件中為 module 添加如下内容

```json
"requestPermissions": [  
  {  
    "name": "ohos.permission.INTERNET"  
  }  
],
```

#### 列表分組組件

```typescript
class GroupList{  
  groupName:string  
  groupArray:Array<string>  
}  
  
@Component  
export default struct Test17{  
  
  @State  
  student:Array<GroupList> = [  
      {  
        groupName:"1",  
        groupArray:["1a","1b","1c","1d","1e","1f"]  
      },  
      {  
        groupName:"2",  
        groupArray:["2a","2b","2c","2d","2e","2f"]  
      },  
      {  
        groupName:"3",  
        groupArray:["3a","3b","3c","3d","3e","3f"]  
      }  
    ]  
  
  @State  
  openArray:Array<Boolean> = [true, true, true]  
  
  @Builder  
  groupBar(item:GroupList, index:number){  
    Row(){  
      Text(item.groupName)  
    }.backgroundColor("#FDBB").width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
    .margin({bottom:10}).onClick(() => {  
      this.openArray[index] = !this.openArray[index]  
    })  
  }  
  
  
  build(){  
    Column(){  
      Row(){  
        Text("分组统计").fontColor("#FFF").fontSize(20)  
      }.backgroundColor("#F661").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      List(){  
        ForEach(this.student, (groupItem, groupIndex) => {  
          ListItemGroup({header:this.groupBar(groupItem,groupIndex)}){  
            if(this.openArray[groupIndex]){  
              ForEach(groupItem.groupArray, (item, index) => {  
                ListItem(){  
                  Row(){  
                    Text(index.toString())  
                    Text(item)  
                  }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
                  // .border({width:{bottom:1},color:"#AAA"})  
                }  
              })  
            }  
          }// .divider({strokeWidth:1, color:"#AAA"})  
        })  
      }.height("95%")  
  
    }.width("100%").height("100%")  
  }  
}
```

### 佈局控件

#### 層曡佈局組件

```typescript
@Component  
export default struct Test18{  
  build(){  
    Column(){  
      Row(){  
        Text("层叠布局").fontSize(20).fontColor("#FFF")  
      }.backgroundColor("#FA11").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Scroll(){  
  
        Column(){  
          Stack(){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.Start}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.End}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.Top}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.Bottom}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.TopStart}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.TopEnd}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.BottomStart}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
  
          Stack({alignContent:Alignment.BottomEnd}){  
            Circle().width(150).height(150).colorBlend("#F00")  
            Circle().width(100).height(100).colorBlend("#fbea")  
            Circle().width(50).height(50).colorBlend("#f59a")  
          }  
        }      }.height("95%")  
    }.width("100%").height("100%")  
  }  
}
```

這一段可謂是對stack的使用列出了非常詳盡的展示啊😂

元素重叠位置可以參考Alignment后屬性名的字面意思

#### 網格佈局組件

```typescript
class StudentInfo{  
  id:string  
  name:string  
  email:string  
}  
@Component  
export default struct Test19{  
  @State  
  studentArray:Array<StudentInfo> = [  
    {id:" 1 ",name:" zhangsan1 ",email:" zhangsan1@qq.com "},  
    {id:" 2 ",name:" zhangsan2 ",email:" zhangsan2@qq.com "},  
    {id:" 3 ",name:" zhangsan3 ",email:" zhangsan3@qq.com "},  
    {id:" 4 ",name:" zhangsan4 ",email:" zhangsan4@qq.com "},  
    {id:" 5 ",name:" zhangsan5 ",email:" zhangsan5@qq.com "},  
    {id:" 6 ",name:" zhangsan6 ",email:" zhangsan6@qq.com "},  
    {id:" 7 ",name:" zhangsan7 ",email:" zhangsan7@qq.com "},  
    {id:" 8 ",name:" zhangsan8 ",email:" zhangsan8@qq.com "},  
    {id:" 9 ",name:" zhangsan9 ",email:" zhangsan9@qq.com "},  
  ]  
  
  build(){  
    Column(){  
      Row(){  
        Text("网格布局").fontColor("#fff").fontSize(20)  
      }.width("100%").justifyContent(FlexAlign.Center).backgroundColor("#f9c0").padding(10)  
  
      Search({placeholder:"请输入条件"}).padding(10)  
  
      Grid(){  
        ForEach(this.studentArray, (item:StudentInfo, index) => {  
          GridItem(){  
            Column(){  
              Row(){  
                Text("编号")  
                Text(item.id)  
              }.width(130).justifyContent(FlexAlign.SpaceBetween).padding(10)  
  
              Row(){  
                Text("姓名")  
                Text(item.name)  
              }.width(130).justifyContent(FlexAlign.SpaceBetween).padding(10)  
  
              Row(){  
                Text("邮箱")  
                Text(item.email).fontSize(7)  
              }.width(130).justifyContent(FlexAlign.SpaceBetween).padding(10)  
  
            }.width(130).height(140).border({width:1}).margin({bottom:10})  
            .justifyContent(FlexAlign.SpaceAround)  
          }  
        })  
      }.height("90%").columnsTemplate("1fr 1fr 1fr")  
    }.width("100%").height("100%")  
  }  
}
```

對於某人來説，網格佈局是個人開發時最愛用的，這是因爲grid不僅能夠快速劃分出大致定位區域，還很適合進行響應式佈局

不過在ArkUI中，單純的Grid和GridItem不太能快速做出類似WPF一樣靈活的分塊操作

例如，Grid對不同格的劃分是以“fr”為單位，從IDE自帶API Reference中推測，似乎只能做到根據數值等分，無法限定其中一部分然後令其他按比例劃分

#### 側面工具條

```typescript
@Component  
export default struct Test20{  
  build(){  
    Column(){  
      Row(){  
        Text("侧面工具栏").fontSize(20).fontColor("#fff")  
      }.backgroundColor("#f182").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      SideBarContainer(SideBarContainerType.Overlay){  
        Column(){  
          Text("用户管理")  
          Text("角色管理")  
          Text("配置管理")  
          Text("日志管理")  
        }.backgroundColor(Color.Gray).justifyContent(FlexAlign.SpaceEvenly)  
  
        Column(){  
          Text("内容显示 内容显示 内容显示 内容显示 内容显示 内容显示")  
        }.height("100%").justifyContent(FlexAlign.Center)  
      }.height("100%").minSideBarWidth(50).sideBarWidth(90).maxSideBarWidth(150)  
      .showSideBar(false)  
      .controlButton({top:10,left:10})  
      .sideBarPosition(SideBarPosition.End)  
      .autoHide(false)  
  
    }.width("100%").height("100%")  
  }  
}
```

#### 路由表組件

```typescript
import router from '@ohos.router'  
@Component  
export default struct Test21{  
  build(){  
    Column(){  
      Row(){  
        Text("路由表").fontColor("#fff").fontSize(20)  
      }.backgroundColor("#f290").width("100%").padding(10).justifyContent(FlexAlign.Center)  
  
      Row(){  
        Navigator({target:"pages/UserPage"}){  
          Text("用户管理")  
        }  
  
        Navigator({target:"pages/RolePage",type:NavigationType.Replace}){  
          Text("角色管理")  
        }  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Row(){  
        Button("用户管理").onClick(() => {  
          router.pushUrl({url:"pages/UserPage"})  
        })  
  
        Button("角色管理").onClick(() => {  
          router.replaceUrl({url:"pages/RolePage"})  
        })  
      }.width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
    }.width("100%").height("100%")  
  }  
}
```

頁面跳轉是非常重要的功能，該示例展示了 Navigator 的用法

可以看到，Navigator 實現跳轉的邏輯還是使用一個棧來管理路由中的頁面，默認就是將 target 所指的頁面直接壓入棧，這一點做法和 Android 以及 xamarin 采用的策略都是基本一致的

在目錄中新建兩個 page 如下

```typescript
import router from '@ohos.router'  
@Entry  
@Component  
struct RolePage {  
  @State message: string = '角色管理'  
  
  build() {  
    Column(){  
      Row(){  
        Row(){  
          Text("角色管理").fontSize(20).fontColor("#fff").margin({right:20})  
            .onClick(() => {  
              router.back()  
            })  
        }.flexGrow(1).justifyContent(FlexAlign.Center)  
  
      }.backgroundColor("#ff0").width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Text(this.message).fontSize(50).fontWeight("100%")  
    }  
  }}
```

```typescript
import router from '@ohos.router'  
@Entry  
@Component  
struct UserPage {  
  @State message: string = '用户管理'  
  
  build() {  
    Column(){  
      Row(){  
        Row(){  
          Text("用户管理").fontSize(20).fontColor("#fff").margin({right:20})  
            .onClick(() => {  
              router.back()  
            })  
        }.flexGrow(1).justifyContent(FlexAlign.Center)  
  
      }.backgroundColor("#ff0").width("100%").padding(10).justifyContent(FlexAlign.SpaceBetween)  
  
      Text(this.message).fontSize(50).fontWeight("100%")  
    }  
  }}
```

此時查看 entry/src/main/resources/base/profile 中的 main_pages.json 内容

```json
{  
  "src": [  
    "pages/Index",  
    "pages/UserPage",  
    "pages/RolePage"  
  ]  
}
```

可以看到新頁面的 URI 已經被自動添加到 src 下了，這裏就是 target 需要指向的路徑了

## 個人總結（吐槽）

將近一周的開發，差不多也認識了ArkUI的基本使用邏輯了

不管某人是否認同，但是ArkUI確實有便捷之處

可以很明顯地感覺到，在使用ArkUI的時候，很多的控件的屬性和方法都具有較高的共性，甚至某人原本第一反應是使用模型綁定加自寫方法實現的邏輯也可以找到封裝好的基本方法。不是説明其豐富性高，某人猜測可能是將UI都以一塊在熒幕上渲染的圖形為基礎，而不是將其作爲類的結果。這樣的優勢也非常明顯——就算某人現在認識的控件不多，也可以把一個基本方法套用在很多不認識或者功能看上去完全沒關係的控件上（就算實在不行了，那不還有擴展方法和模型綁定麽😂）