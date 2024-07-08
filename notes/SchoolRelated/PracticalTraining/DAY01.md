# 暑期實訓 其之一

## 前言

接[上文](./DAY00.html)

此爲實訓第一天内容，首先總結一下今日事項，主要概括為以下幾方面

- 項目介紹與宣講
- 環境搭建等前置技能
- 熟悉基本ArkUI開發

## 主要内容

### 項目介紹與宣講

這部分主要就是針對接下來的安排與發生的調整做出説明，沒什麽值得記錄的點
### 環境搭建等前置技能

#### 集成開發環境

此次實訓采用 Deveco Studio 3.10.501版本進行鴻蒙應用的開發，若采用其他版本，某人無法確保該條目下所有方法可行🙏

1. 安裝Deveco Studio 
	
	Deveco Studio 是一款用於開發鴻蒙應用的 IDE ，支持 ArkTS 編寫（此後簡稱deveco）
	
	deveco 可以從華爲官網下載，注意該 IDE 可能需要使用華爲賬號來獲取，因此請先注冊賬號
	
2. 安裝 node.js 和 ohpm 
	
	OHPM（OpenHarmony Package Manager）是一个用于管理 OpenHarmony 项目依赖的包管理工具
	
	安裝 deveco 后會提示導入或者安裝這兩個工具，如果您忘了設置，可以在 `Setting > Build,Extention,Deployment` 中找到相應的修改選項
	
	>小插曲
	>在本次實訓中，某人携帶了一臺長期閑置的筆電，由於曾經使用過 node.js ，於是希望引用本地的 node 。然而 deveco 中對文件路徑有著近乎變態的嚴格要求，導致某人最終只能選擇按照其傻瓜式操作一鍵安裝 	
	
3. 安裝鴻蒙 OS 的 SDK 
	
	目前使用到兩個主要的 SDK 
	
	- hmscore
	- openharmony
	
	相關配置選項可以在 `Setting > SDK` 中找到
	
4. 安裝模擬器
	
	完成以上步驟之後，應該可以在 deveco 中按照項目模板進行創建。若無法完成該動作，請檢查此前步驟，有問題請優先查閲華爲官方提供的文檔以及相關社區内容
	
	創建項目后無法直接運行得到直觀的結果，此時在運行鍵左側點開下拉框，選擇 Device Manager ，根據需求安裝對應的模擬器
#### 版本控制工具

在示例中采用 TortoiseGit 作爲版本控制工具

由於已安裝 GitHub Desktop ，故未采用該方式

鑒於目擊多起 git 工具使用失敗案例，下面給出大致操作步驟供參考

1. 安裝 git
	
	無論是TortoiseGit 還是 GitHub Desktop ，其實都可以算是 git 的魔改，推薦極簡主義者安裝 git ，推薦無腦主義者安裝 GitHub Desktop，搜索關鍵字參考“{您使用的軟體} + 使用步驟 + 教你學會 + 倉庫拉取”
	
2. 獲取本機密鑰
	
	如果您是第一次使用 git ，請通過命令生成專屬於本機的密鑰，搜索關鍵字參考“ `rsa` + `ssh` + `git` | `ssh密鑰`”
	
	（Windows中）正常得到密鑰后應該在`C://Users/{您的用戶名}/.ssh`路徑下找到兩個文件
	
	- `id_rsa`
	- `id_rsa.pub`
	
3. 上傳密鑰
	
	來到您倉庫所在位置，不論是常見的GitHub，Gitee還是自建的GitLab之類的，都會提供ssh密鑰上傳的功能，通常位於`倉庫設置`、`倉庫屬性`等叫法的地方
	
	找到通常名爲`ssh公鑰`或`ssh密鑰`等條目下，添加一條記錄，形式爲 標題-值 的鍵值對，值為上一步中`id_rsa.pub`裏的内容，請確保以 rsa 開頭
	
	>注意，上傳`id_rsa.pub`的作用是讓倉庫知道，用您手上這臺電腦操作倉庫和您賬號操作是等價的，這樣才能無感使用git工具（這一步忽略者較多，當然用賬號密碼登錄也行，但是使用git時好像會直接回顯權限錯誤）
	
4. 拉取倉庫
	
	在目標文件夾位置運行
	
	git clone {目標倉庫}
	
	這行命令一般會在倉庫醒目位置標出
	
### 熟悉基本ArkUI開發（分析説明之類的，有時間再補充）

在以上步驟完成之後，可通過deveco創建一個空項目，選項均保持默認即可

某人創建了一個名爲**PracticalTraining**的項目，創建後將默認打開文件Index.ets

關於文件目錄的詳細信息請參照華爲相關文檔或等待某人後文的補充

#### HelloWorld

在Index.ets中的代碼是一個默認的 HelloWorld ，其中包含注解

```java
@Entry  
@Component 
```

這説明了將結構體（暫且這麽叫）作爲一個組件來使用，此處當然是根組件了

在結構體裏面，可以定義變量，使用如下句型

```typescript
@State label:string = '用户名称';
@State name:string = '张三';
```

采用了神似Vue的build()，基本佈局可以放在裏面

常用了Row和Column，常用屬性有wicth()和height()等，使用方法參考華爲提供的文檔

#### 子組件 定義

前面所操作的内容均爲 Index.ets文件中的代碼，此時將其作爲一個根組件使用

子組件則是將某個組件引用到其他組件中，從而實現以組件為單位的代碼復用

1. 創建新組件

	在 ets 檔案夾中創建新的檔案夾，我將其命名為 components（和pages同級）
	
	在 components 檔案夾下新增 ets 文件，我將其命名為Test1.ets
	
2. 編輯代碼

```java
@Styles function rowStyle(){  
  .padding(10).width("100%").border({width:{bottom:1},color:"#EEE"})  
}  
  
@Extend(Row) function rowBlock(){  
  .justifyContent(FlexAlign.SpaceBetween)  
}  
  
@Component  
export default struct Test1{  
  build() {  
    Column() {  
      Column() {  
        Row() {  
          Text("名字")  
          Text("GZ4nna")  
        }.rowBlock().rowStyle()  
  
        Row() {  
          Text("账户")  
          Text("GZ4nna")  
        }.rowBlock().rowStyle()  
  
        Row() {  
          Text("年龄")  
          Text("" + 123)  
        }.rowBlock().rowStyle()  
  
      }.backgroundColor("#FFF")  
    }.width("100%").height("100%").backgroundColor("#AAA")  
    .justifyContent(FlexAlign.Center)  
  }  
}
```

3. 作爲子組件引用

```java
import Test from "../components/Test1"  
  
@Entry  
@Component  
struct Index {  
  build() {  
      Column() {  
        Test()  
      }.width("100%").height("100%")  
  }  
}
```

#### 子組件 單向傳值

子組件光是作爲固定的視圖使用的話，就像是一塊死物放在那裏做展示，我們希望組件也是可以參與交互過程的

從創建子組件的過程中可以看出來，組件本質上也是一個對象罷了，那麽在一個類中引用其他類的對象，對其屬性做出操作，不就可以使子組件有辦法響應父組件的行爲了麽

1. 創建子組件
	
	在 components 檔案夾下新增 ets 文件，我將其命名為Test2.ets
	
2. 編輯代碼

```java
@Component  
struct Person{  
  label:string  
  value:string  
  
  build(){  
    Row(){  
      Text(this.label)  
      Text(this.value)  
    }.padding(10).width("100%").justifyContent(FlexAlign.SpaceBetween)  
    .border({width:{bottom:1},color:"#EEE"})  
  }  
}  
  
@Component  
export default struct Test2{  
  build() {  
    Column() {  
      Person({label:"名字",value:"张三"})  
    }.width("100%").height("100%")  
  }  
}
```

3. 在父組件中傳值
	
	修改引用
```java
import Test from "../components/Test2"
```

#### 子組件 雙向傳值

在現代的應用程式中，講究視圖和數據的響應式，比如`MVVM`模式和`MVU`模式等，而實現這些現代化模式的基礎就是實現了數據的雙向綁定

對於子組件來説，能夠實現雙向傳值是必要的，這決定了父組件能否將散發后的行爲匯聚起來，當然，目前討論的重點在於確實將一個值在父組件和子組件閒傳遞

1. 創建子組件
2. 編輯代碼

```java
@Component  
struct Person{  
  @Link username:string  
  @Link age:number  
  
  build(){  
    Column(){  
      Row(){  
        Text("子组件")  
      }.width("100%").padding(10)  
      .justifyContent(FlexAlign.Center).backgroundColor("#b1ae65")  
  
      Column(){  
        Row(){  
          Text("姓名")  
          TextInput({text:this.username}).onChange((val)=>{  
            this.username = val  
          }).flexBasis(220).backgroundColor("#FFF")  
        }  
        .width("100%").padding({left:10,right:10,top:10,bottom:3})  
        .justifyContent(FlexAlign.SpaceBetween)  
        .border({width:{bottom:1},color:"#EEE"})  
  
        Row(){  
          Text("年龄")  
          TextInput({text:this.age + ""}).onChange((val)=>{  
            this.age = parseInt(val)  
          }).flexBasis(220).backgroundColor("#FFF").type(InputType.Number)  
        }.width("100%").padding({left:10,right:10,top:10,bottom:3})  
        .justifyContent(FlexAlign.SpaceBetween).border({width:{bottom:1},color:"#EEE"})  
      }.flexGrow(1)  
    }  
    .width(300).height(150).border({width:1})  
  }  
}  
```

1. 編輯父組件

```java
@Component  
export default struct Test3{  
  @State username:string = "张三"  
  @State age:number = 23  
  @State sex:string = "张三"  
  
  build(){  
    Column(){  
      Column(){  
        Row(){  
          Text("父组件")  
        }.width("100%").padding(10)  
        .justifyContent(FlexAlign.Center).backgroundColor("#0F0")  
  
        Column(){  
          Row(){  
            Text("姓名")  
            TextInput({text:this.username}).onChange((val)=>{  
              this.username = val  
            }).flexBasis(220).backgroundColor("#FFF")  
          }.width("100%").padding({left:10,right:10,top:10,bottom:3})  
          .justifyContent(FlexAlign.SpaceBetween).border({width:{bottom:1},color:"#EEE"})  
  
          Row(){  
            Text("年龄")  
            TextInput({text:this.age + ""}).onChange((val)=>{  
              this.age = parseInt(val)  
            }).flexBasis(220).backgroundColor("#FFF").type(InputType.Number)  
          }.width("100%").padding({left:10,right:10,top:10,bottom:3})  
          .justifyContent(FlexAlign.SpaceBetween).border({width:{bottom:1},color:"#EEE"})  
        }.flexGrow(1)  
      }.width(300).height(150).border({width:1})  
  
      Person({username:$username,age:$age})  
  
      Person({username:$username,age:$age})  
    }.width("100%").height("100%").justifyContent(FlexAlign.SpaceEvenly)  
  }  
}
```




後面沒好沒好。。。


父组件直接点出属性，可以连续添加
比如
```typescript
Row(){
	
}.padding().width().balabala......
```

通过扩展方法实现大量重复属性的继承
```java
@Extend(Row){
	.padding().width().balabala.....
}
```

在Extend注解里面是需要被扩展的组件类型，将点出来的属性放在内容中

## 個人總結（吐槽）