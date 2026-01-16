#  暑期實訓 其之十一

## 前言

**涉及的想法僅為個人觀點，甚至日後會修改也不一定，請務必批判而不是認同**

從第三周開始，需要運用此前通過示例學會的組件知識

機構提供了一個學習平臺，並同步提供一系列api供調用，現在需要開發一個應用對接該平臺，稱作雲課堂項目

需要擁有的story如下

| Story编号 | Story模块 | Story名称 | story描述                                                          |
| ------- | ------- | ------- | ---------------------------------------------------------------- |
| 1       | 用户模块    | 用户登录    | 用户名、密码、验证滑动                                                      |
| 2       | 用户模块    | 用户注册    | 邮箱、验证码、用户密码、确认密码                                                 |
| 3       | 用户模块    | 忘记密码    | 邮箱、验证码、用户密码、确认密码                                                 |
| 4       | 课程模块    | 全部课程    | 公开课程和定制课程(学生和班级、课程相关联)                                           |
| 5       | 课程模块    | 公开课程    | 公开课程                                                             |
| 6       | 课程模块    | 定制课程    | 定制课程(学生和班级、课程关联)                                                 |
| 7       | 课程模块    | 课程目录    | 章节和计划，计划下可以关联视频、文档                                               |
| 8       | 课程模块    | 课程评价    | 5星评分、点赞、留言                                                       |
| 9       | 课程模块    | 我的笔记    | 课程、学生相关联，留言                                                      |
| 10      | 课程模块    | 视频播放    | 全屏、开始、暂停、进度条                                                     |
| 11      | 课程模块    | 文档预览    | 文档预览                                                             |
| 12      | 班级模块    | 全部班级    | 公开班级、我的班级((学生和班级相关联)                                             |
| 13      | 班级模块    | 我的班级    | 我的班级((学生和班级相关联)                                                  |
| 14      | 班级模块    | 班级详情    | 班主任、学员数量、班级简介、班级名称、加入班级                                          |
| 15      | 我的模块    | 个人列表    | 个人图标、用户名、班级名称、二维码、学习计划、测评记录、学习记录、证书、机构注册与服务协议、班级信息处置规范、隐私政策、常见问题 |
| 16      | 我的模块    | 修改人个信息  | 修改 用户名称、邮箱、电话、昵称                                                 |

## 主要内容

### 總體設計

#### 應用結構

某人打算沿用此前使用MAUI進行安卓開發時候的思路，使用MVVM模式進行前端頁面開發，并在必要時候通過發送http請求來獲取後端web api提供的各種數據來豐富展示數據

想要結構合乎MVVM，除了最顯而易見的對視圖和視圖模型進行數據綁定外，需要合理設計模型。通過分析story要求，認爲所有動作均是圍繞`用戶`，`課程`，`班級`三者發出的，因此首先設計了`User`、`Course`、`Class`這三個模型，但是暫時不定義任何内容，僅作爲占位

#### 設計思想

應前端後端化趨勢，這裏嘗試通過story描述功能對所有動作進行領域劃分

但是認爲在此處使用領域思想不夠合適，因爲各個部分雖然在代碼上可以視作獨立的塊，但是在邏輯上關係緊密。比如：不管是課程還是班級，總得依賴於用戶才行

最後我認爲合適的方式是使用一個單例來存放必要資源，所有模塊各自代碼獨立，通過到該統一資源存放點自取所需必要内容來支持自己的運行，這種方式在當前場景下兼顧了效率和便捷性

### 前期工作

決定好了總體的方向后，開始著手準備前期的工作

#### 資源管理容器

這裏的資源指的是運行過程中在内存中產生的實例，通過簡單的查閲后，沒有發現 ArkTS 提供 DI 容器，這對於 MVVM 來説是非常不利的。剛好此前也提到了有統一管理資源的需求，所以嘗試構造以下類型：

```typescript
export default class ServiceContainHelper {  
  private static instances: Map<string, any> = new Map();  
  
  static register<T>(key: string, instance: T) {  
    this.instances.set(key, instance);  
  }  
  
  static resolve<T>(key: string): T {  
    return this.instances.get(key);  
  }  
  
  static contain(key: string): boolean {  
    return this.instances.has(key);  
  }  
  
  static remove(key: string) {  
    this.instances.delete(key)  
  }  
}
```

以上類型提供了一個容器，可以通過鍵值來存放任意實例，并可以手動注冊、獲取、刪除

此後的開發中，不管是依賴還是邏輯上的單例，都可以通過這個容器來進行管理

弊端也很明顯，超級容易越放越多，只適合小項目這樣不嚴謹地玩，此後需要在開發時注意這個問題

#### 網絡請求幫助

由於所有數據需要從平臺提供的api獲取，所以首先實現一個幫助類，爲我們的請求提供支持：

```typescript
import http from '@ohos.net.http'  
  
  
// cloud class address  
const BASE_ADDRESS = "http://123.60.25.147:8090"  
  
// entity of ajax format request  
class AjaxEntity{  
  // path of api, don't add base url and port  
  url: string  
  
  // params in http request  
  extraData: Array<ExtraDataEntity>  
  
  // function used when request success  
  success: (data: Object) => void  
  
  // function used when request fail  
  fail: (data: Object) => void  
  
  
  // necessary need url and extraData  
  constructor(url: string,extraData:  
    Array<ExtraDataEntity>,success:(data:Object)=>void,fail:(data:Object)=>void) {  
    this.url = url;  
    this.extraData = extraData;  
    this.success = success;  
    this.fail = fail;  
  }  
}  
  
// entity of extra data  
// when you send a http request, should take some key-value pair as ExtraDataEntity format  
// always used like {name: "key", value: value} to set a entity in other constructor  
class ExtraDataEntity{  
  name: string  
  value: string|number|boolean  
  
  constructor(name: string, value: string) {  
    this.name = name  
    this.value = value  
  }  
}  
  
// http request helper  
export default class HttpClientHelper{  
  
  static send(value:AjaxEntity):Promise<void>{  
    return new Promise((resolve,reject) => {  
      HttpClientHelper.sendHttpMsg(value)  
    })  
  }  
  
  // emulate send action  
  // return a  http response  
  static sendHttpMsg(value: AjaxEntity) {  
    let httpClient = http.createHttp()  
  
    httpClient.on('headersReceive', (header) => {  
      console.info('header: ' + JSON.stringify(header));  
    });  
  
    httpClient.request(  
      BASE_ADDRESS + value.url,  
      {  
        method: http.RequestMethod.POST,  
        header: {  
          'Content-Type': 'application/x-www-form-urlencoded'  
        },  
        extraData: HttpClientHelper.changeWWWParam(value.extraData),  
        expectDataType: http.HttpDataType.STRING,  
        usingCache: true,  
        priority: 1,  
        connectTimeout: 60000,  
        readTimeout: 60000,  
        usingProtocol: http.HttpProtocol.HTTP1_1,  
      }, (err, data) => {  
        if (!err) {  
          if(data.result){  
            try{  
              var rst:string = data.result.toString();  
              value.success(JSON.parse(rst))  
            }catch(error){  
              console.info("error:" + JSON.stringify(error));  
            }  
          }          else{  
            value.success(null)  
          }  
        } else {  
          value.fail(err)  
        }  
      httpClient.off('headersReceive');  
      httpClient.destroy();  
      }  
    )  
  }  
  
  // convert ExtraDataEntity type to http request param  
  static changeWWWParam(dataArray:Array<ExtraDataEntity>){  
    var rst:string = '';  
  
    dataArray.forEach((item)=>{  
      rst+=item.name + "=" + item.value + "&"  
    })  
  
    // remove last "&"  
    if(rst !== ''){  
      rst = rst.substring(0, rst.length - 1)  
    }  
  
    console.info("changeExtraDataWWW:" + rst)  
    return rst  
  }  
}
```

ExtraDataEntity：

ExtraDataEntity 類是爲了存放置於請求中的報文參數

AjaxEntity：

AjaxEntity 類模擬了一個ajax請求體

在最初的設計中，不僅對於所有成員，還提供了請求類型、報頭等等屬性的自定義設置，但是在長時間的對 HttpClientHelper 類的調試中最終捨棄了這些内容

HttpClientHelper：

HttpClientHelper 類型是負責 http 請求的幫助類

在 HttpClientHelper 中提供了一個 send 方法，接受一個 AjaxEntity 類型參數，並提供對 sendHttpMsg 方法的包裝調用。這是因爲 sendHttpMsg 方法在執行時由於其異步性質會導致調試時很難看到執行結果，封裝后可以通過 async/await 來方便地等待執行結束后進行下一步的調試

> 一開始某人認爲可以將 ExtraDataEntity 和 AjaxEntity 獨立放在其他文件夾中進行進一步的結構管理，但是很遺憾的是會出現衆多不可名狀的錯誤，最後發現使用單文件可以一勞永逸杜絕各種問題

### 開始開發

測試部分省略后，接下來快速開發出模板，作爲後續所有開發的依據

#### model 模板

```typescript
import IUser from "./IUser"  
import UserInfo from './UserInfo'  
  
@Observed  
export default class User implements IUser{  
  userUuid:string  
  userInfo:UserInfo  
}
```

按照下面規則編寫

- 在接口中確保只聲明最重要的内容
- 在主要 model 類中按照最直觀的邏輯設計
- 能封裝成類就不要直接使用字段，這利好後續變更

#### viewmodel 模板

```typescript
import router from '@ohos.router';  
import HttpClientHelper from '../../helpers/HttpClientHelper';  
import ServiceContainHelper from '../../helpers/ServiceContainHelper';  
import ForgetPasswordMsg from '../../misc/ForgetPasswordMsg';  
import LoginMsg from '../../misc/LoginMsg';  
import RegisterUserMsg from '../../misc/RegisterUserMsg';  
import User from '../models/User';  
import UserInfo from '../models/UserInfo';  
  
  
@Observed  
export default class LoginPageViewModel{  
  
  /*  
   * status flags   
   * */  
   
  // user login status, false is not login yet  
  // must put instance into service container before change to true  
  // must route into index page after change to true  
  statusFlagUserLoginStatus: boolean = false  
  public get StatusFlagUserLoginStatus(): boolean {  
    return this.statusFlagUserLoginStatus;  
  }  
  public set StatusFlagUserLoginStatus(value:boolean) {  
    ServiceContainHelper.register("user",this.user)  
    ServiceContainHelper.register("userInfo",this.userInfo)  
    this.statusFlagUserLoginStatus = value  
    router.back()  
  }  
  
  /*  
   * request params   
   * */  
  
  // user name for user login  requestParamUserLoginFunctionParamUserName: string = ""  
  public get RequestParamUserLoginFunctionParamUserName(): string {  
    return this.requestParamUserLoginFunctionParamUserName  
  }  
  public set RequestParamUserLoginFunctionParamUserName(value: string) {  
    this.requestParamUserLoginFunctionParamUserName = value  
  }  
  
  // user password for user login  
  requestParamUserLoginFunctionParamPassword: string = ""  
  public get RequestParamUserLoginFunctionParamPassword(): string {  
    return this.requestParamUserLoginFunctionParamPassword  
  }  
  public set RequestParamUserLoginFunctionParamPassword(value: string) {  
    this.requestParamUserLoginFunctionParamPassword = value  
  }  
  
  // email for user register  
  requestParamUserRegisterFunctionParamEmail: string = ""  
  public get RequestParamUserRegisterFunctionParamEmail(): string {  
    return this.requestParamUserRegisterFunctionParamEmail  
  }  
  public set RequestParamUserRegisterFunctionParamEmail(value: string) {  
    this.requestParamUserRegisterFunctionParamEmail = value  
  }  
  
  // password for user register  
  requestParamUserRegisterFunctionParamPassword: string = ""  
  public get RequestParamUserRegisterFunctionParamPassword(): string {  
    return this.requestParamUserRegisterFunctionParamPassword  
  }  
  public set RequestParamUserRegisterFunctionParamPassword(value: string) {  
    this.requestParamUserRegisterFunctionParamPassword = value  
  }  
  
  // email for forget password  
  requestParamForgetPasswordFunctionParamEmail: string = ""  
  public get RequestParamForgetPasswordFunctionParamEmail(): string {  
    return this.requestParamForgetPasswordFunctionParamEmail  
  }  
  public set RequestParamForgetPasswordFunctionParamEmail(value: string) {  
    this.requestParamForgetPasswordFunctionParamEmail = value  
  }  
  
  // password for forget password  
  requestParamForgetPasswordFunctionParamPassword: string = ""  
  public get RequestParamForgetPasswordFunctionParamPassword(): string {  
    return this.requestParamForgetPasswordFunctionParamPassword  
  }  
  public set RequestParamForgetPasswordFunctionParamPassword(value: string) {  
    this.requestParamForgetPasswordFunctionParamPassword = value  
  }  
  
  /*  
   * response messages   
   * */  
  
  // user login request's result  
  // see misc/LoginMsg for detailed structure  
  responseMessageLoginMessage: LoginMsg = new LoginMsg()  
  public get ResponseMessageLoginMessage(): LoginMsg {  
    return this.responseMessageLoginMessage  
  }  
  public set ResponseMessageLoginMessage(value: LoginMsg) {  
    this.responseMessageLoginMessage = value  
  }  
  
  // user register request's result  
  // see misc/RegisterUserMsg for detailed structure  
  responseMessageRegisterMessage: RegisterUserMsg = new RegisterUserMsg()  
  public get ResponseMessageRegisterMessage(): RegisterUserMsg {  
    return this.responseMessageRegisterMessage  
  }  
  public set ResponseMessageRegisterMessage(value: RegisterUserMsg) {  
    this.responseMessageRegisterMessage = value  
  }  
  
  // forget password request's result  
  // see misc/ForgetPasswordMsg for detailed structure  
  responseMessageForgetPasswordMessage: ForgetPasswordMsg = new ForgetPasswordMsg()  
  public get ResponseMessageForgetPasswordMessage(): ForgetPasswordMsg {  
    return this.responseMessageForgetPasswordMessage  
  }  
  public set ResponseMessageForgetPasswordMessage(value: ForgetPasswordMsg) {  
    this.responseMessageForgetPasswordMessage = value  
  }  
  
  //  
  
  /*   
  * models   
  * */  
  
  user: User = new User()  
  userInfo: UserInfo = new UserInfo()  
  
  
  // login function  
  async UserLoginFunction(){  
    await HttpClientHelper.send({  
      url:"/api/user/login",  
      extraData:[  
        {name:"userName",value:this.RequestParamUserLoginFunctionParamUserName},  
        {name:"password",value:this.RequestParamUserLoginFunctionParamPassword}  
      ],  
      success:(data:LoginMsg) => {  
        this.ResponseMessageLoginMessage = data  
      },  
      fail:() => {}  
    })  
  }  
  
  async UserRegisterFunction(){  
    await HttpClientHelper.send({  
      url:"/api/user/registerUserByEmail",  
      extraData:[  
        {name:"email",value:this.RequestParamUserRegisterFunctionParamEmail},  
        {name:"password",value:this.RequestParamUserRegisterFunctionParamPassword}  
      ],  
      success:(data:RegisterUserMsg) => {  
        this.ResponseMessageRegisterMessage = data  
      },  
      fail:() => {}  
    })  
  }  
  
  async ForgetPassword(){  
    await HttpClientHelper.send({  
      url:"/api/user/forgetPasswdByEmail",  
      extraData:[  
        {name:"email",value:this.RequestParamForgetPasswordFunctionParamEmail},  
        {name:"password",value:this.RequestParamForgetPasswordFunctionParamPassword}  
      ],  
      success:(data:RegisterUserMsg) => {  
        this.ResponseMessageForgetPasswordMessage = data  
      },  
      fail:() => {}  
    })  
  }  
  
}
```

按照下面規則編寫

- 按照功能置於不同位置，在顯眼処劃分
- 盡量添加描述性注釋，就算是很直觀的内容
- 命名盡量按照：主要涉及功能+主要涉及方法+主要涉及參數，杜絕簡寫縮寫
- 對所有成員添加訪問器，在 view 中統一通過訪問器訪問

#### view 模板

```typescript
import LoginPageViewModel from '../mvvm/viewModels/LoginPageViewModel'  
@Entry  
@Component  
struct LoginPage {  
  @State  
  viewModel: LoginPageViewModel = new LoginPageViewModel()  
  
  build() {  
    Column(){  
      Column(){  
        Text("邮箱")  
        TextInput({text: this.viewModel.RequestParamUserRegisterFunctionParamEmail})  
      }.justifyContent(FlexAlign.SpaceBetween).padding(10)  
      Column(){  
        Text("密码")  
        TextInput({text: this.viewModel.RequestParamUserRegisterFunctionParamPassword})  
      }.justifyContent(FlexAlign.SpaceBetween).padding(10)  
      Column(){  
        Button("注册账号").onClick(() => {  
          this.viewModel.UserRegisterFunction()  
        })  
      }.justifyContent(FlexAlign.SpaceBetween).padding(10)  
    }  
  }}
```

按照下面規則編寫

- 盡量只使用 viewmodel 提供的數據

雖然這是一個 Page 例子，但是實際上其他頁面都是通過 tabs 組件在主頁上切換不同的 component 實現的，考慮到未登錄時既然不可執行其他動作那麽導航部分也應該是不可見的，所以成爲了一個特例

以上的代碼編寫風格大致符合目前個人開發時所習慣性遵守的，由於在該開發環境中難免需要改變一些細節，并且合作時也需要先統一風格等各種因素，於是首先在開發的第一步完成了可以涵蓋大部分開發内容的幾個類型文件的簡單示例編寫。

### 其他工作

除此之外，主頁的形式也大致確定了

```typescript
import router from '@ohos.router'  
import ClassPage from '../Components/ClassPage'  
import CoursePage from '../Components/CoursePage'  
import UserInfoPage from '../Components/UserInfoPage'  
import ServiceContainHelper from '../helpers/ServiceContainHelper'  
@Entry  
@Component  
struct Index {  
  
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
  
  build() {  
    Column(){  
      // logic reverse  
      if(!ServiceContainHelper.contain("User")){  
        Button("请先登录").onClick(() => {  
          router.pushUrl({url:"./LoginPage"})  
        })  
      }  
      else{  
        Tabs({barPosition:BarPosition.End}){  
          TabContent() {  
            CoursePage()  
          }.tabBar(this.tarBar("课程", 0))  
  
          TabContent() {  
            ClassPage()  
          }.tabBar(this.tarBar("班级", 0))  
  
          TabContent() {  
            UserInfoPage()  
          }.tabBar(this.tarBar("我的", 0))  
        }.onChange((index) => {  
          this.selectIndex = index  
        })  
      }  
    }.width("100%").height("100%")  
    .justifyContent(FlexAlign.Center)  
  }  
}
```

得益於 tabs 組件的便捷切換，可以先將每個模塊作爲一個 component 設計，通過切換組件模擬路由功能實現跳轉。兼顧了多人開發和全局管理

## 感想與總結

本日主要是嘗試將常用的開發方式運用到該項目中

在實際的操作時，遇到了較多的問題

首先是數據綁定，在使用自帶的 對象鏈接 或者 本地存儲 功能時會遇到諸如初始化等問題，最後放棄了大部分的綁定，改爲統一存放資源加手動同步的方式，雖然原始且繁瑣，但總算實現了差不多的功能

其次是網絡請求，由於默認的異步請求會導致返回包數據賦值到本地的順序晚於本地調用，導致測試時無法取出數據，但是卻顯示請求成功。當時完全沒想到異步的問題導致消磨了將近一上午的時間

最後總算是都解決了並給出了一個相對可以讓所有合作者看懂的小示例。不過已有代碼沒有經過任何可行性測試，也沒有完善資源管理和模型定義相關内容，這是接下來在模塊開發之前的必要步驟