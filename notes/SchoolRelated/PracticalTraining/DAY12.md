#  暑期實訓 其之十二

## 前言

**涉及的想法僅為個人觀點，甚至日後會修改也不一定，請務必批判而不是認同**

接[上文](./DAY11.html)

今天除了編寫一些簡單的代碼，優化項目結構，最重要的内容是對於團隊協作和組織多人項目的思考

## 主要内容

完善 LoginViewModel

```typescript
import router from '@ohos.router';  
import HttpClientHelper from '../../helpers/HttpClientHelper';  
import ServiceContainHelper from '../../helpers/ServiceContainHelper';  
import ForgetPasswordMsg from '../../misc/ForgetPasswordMsg';  
import LoginMsg, { LoginMsgObj } from '../../misc/LoginMsg';  
import RegisterUserMsg from '../../misc/RegisterUserMsg';  
import User from '../models/User';  
import GetVerificationCodeMsg from '../../misc/GetVerificationCodeMsg';  
  
  
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
    ServiceContainHelper.register("User",this.user)  
    this.statusFlagUserLoginStatus = value  
    router.replaceUrl({  
      url:"pages/Index",  
      params:{  
        status: this.StatusFlagUserLoginStatus  
      }  
    })  
  }  
  
  // user verify status  
  // check it before user register and forget password  
  statusFlagUserVerifyStatus: boolean = false  
  public get StatusFlagUserVerifyStatus(): boolean {  
    return this.statusFlagUserVerifyStatus;  
  }  
  public set StatusFlagUserVerifyStatus(value:boolean) {  
    this.statusFlagUserVerifyStatus = value  
  }  
  
  // slide verify status  
  // check it before user login  
  statusFlagSlideVerifyStatus: boolean = false  
  public get StatusFlagSlideVerifyStatus(): boolean {  
    return this.statusFlagSlideVerifyStatus;  
  }  
  public set StatusFlagSlideVerifyStatus(value:boolean) {  
    this.statusFlagSlideVerifyStatus = value  
  }  
  
  /*  
   * request params   
   * */  
  
  // user name for user login  
  requestParamUserLoginFunctionParamUserName: string = "zsjyvso8u@mozmail.com"  
  public get RequestParamUserLoginFunctionParamUserName(): string {  
    return this.requestParamUserLoginFunctionParamUserName  
  }  
  public set RequestParamUserLoginFunctionParamUserName(value: string) {  
    this.requestParamUserLoginFunctionParamUserName = value  
  }  
  
  // user password for user login  
  requestParamUserLoginFunctionParamPassword: string = "123456"  
  public get RequestParamUserLoginFunctionParamPassword(): string {  
    return this.requestParamUserLoginFunctionParamPassword  
  }  
  public set RequestParamUserLoginFunctionParamPassword(value: string) {  
    this.requestParamUserLoginFunctionParamPassword = value  
  }  
  
  // email for user register  
  requestParamUserRegisterFunctionParamEmail: string = "zsjyvso8u@mozmail.com"  
  public get RequestParamUserRegisterFunctionParamEmail(): string {  
    return this.requestParamUserRegisterFunctionParamEmail  
  }  
  public set RequestParamUserRegisterFunctionParamEmail(value: string) {  
    this.requestParamUserRegisterFunctionParamEmail = value  
  }  
  
  // password for user register  
  requestParamUserRegisterFunctionParamPassword: string = "123456"  
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
  
  // email for get verification code  
  requestParamGetVerificationCodeFunctionParamEmail: string = ""  
  public get RequestParamGetVerificationCodeFunctionParamEmail(): string {  
    return this.requestParamGetVerificationCodeFunctionParamEmail  
  }  
  public set RequestParamGetVerificationCodeFunctionParamEmail(value: string) {  
    this.requestParamGetVerificationCodeFunctionParamEmail = value  
  }  
  
  /*  
   * response messages   
   * */  
  
  // user login request's result  
  // see misc/LoginMsg for detailed structure  
  responseMessageLoginMessage: LoginMsgObj = new LoginMsgObj()  
  public get ResponseMessageLoginMessage(): LoginMsgObj {  
    return this.responseMessageLoginMessage  
  }  
  public set ResponseMessageLoginMessage(value: LoginMsgObj) {  
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
  
  // get verification code request's result  
  // see misc/ForgetPasswordMsg for detailed structure  
  responseMessageGetVerificationCodeMessage: GetVerificationCodeMsg = new GetVerificationCodeMsg()  
  public get ResponseMessageGetVerificationCodeMessage(): GetVerificationCodeMsg {  
    return this.responseMessageGetVerificationCodeMessage  
  }  
  public set ResponseMessageGetVerificationCodeMessage(value: GetVerificationCodeMsg) {  
    this.responseMessageGetVerificationCodeMessage = value  
  }  
  
  /*  
   * user input   
   * */  
  
  userInputVerificationCode: string = ""  
  public get UserInputVerificationCode(): string {  
    return this.userInputVerificationCode  
  }  
  public set UserInputVerificationCode(value: string) {  
    this.userInputVerificationCode = value  
  }  
  
  /*  
   * models   
   * */  
  
  user: User = new User()  
  
  
  // login function  
  async UserLoginFunction(){  
    console.info("12138--into login")  
    if(this.StatusFlagSlideVerifyStatus){  
      await HttpClientHelper.send({  
        url:"/api/user/login",  
        extraData:[  
          {name:"userName",value:this.RequestParamUserLoginFunctionParamUserName},  
          {name:"password",value:this.RequestParamUserLoginFunctionParamPassword}  
        ],  
        success:(data:LoginMsg) => {  
          this.ResponseMessageLoginMessage = data.obj  
          console.info("12138--msg:"+data.msg+data.success+data.description)  
          if(data.success){  
            console.info("12138--info:"+this.ResponseMessageLoginMessage.userUuid+this.ResponseMessageLoginMessage.email)  
            this.user.userUuid = this.ResponseMessageLoginMessage.userUuid  
            this.StatusFlagUserLoginStatus = true  
          }  
        },  
        fail:(data:LoginMsg) => {  
          console.info("12138--fail")  
          console.info("12138--msg:"+data.msg+data.success+data.description)  
        }  
      })  
    }  
  }  
  
  async UserRegisterFunction(){  
    if(this.StatusFlagUserVerifyStatus){  
      await HttpClientHelper.send({  
        url:"/api/user/registerUserByEmail",  
        extraData:[  
          {name:"email",value:this.RequestParamUserRegisterFunctionParamEmail},  
          {name:"password",value:this.RequestParamUserRegisterFunctionParamPassword}  
        ],  
        success:(data:RegisterUserMsg) => {  
          this.ResponseMessageRegisterMessage = data  
          console.info("12138--"+data.msg+data.success+data.description)  
        },  
        fail:(data:RegisterUserMsg) => {  
          console.info("12138--fail")  
          console.info("12138--"+data.msg+data.success+data.description)  
        }  
      })  
    }  
  }  
  
  async ForgetPassword(){  
    if(this.StatusFlagUserVerifyStatus){  
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
  
  async GetVerifyCode(){  
    await HttpClientHelper.send({  
      url:"/api/user/getVerifyCode",  
      extraData:[  
        {name:"email",value:this.RequestParamGetVerificationCodeFunctionParamEmail}  
      ],  
      success:(data:GetVerificationCodeMsg) => {  
        console.info("12138--get verify code success: " + data.msg + data.success + data.description + data.code)  
        this.ResponseMessageGetVerificationCodeMessage = data  
      },  
      fail:() => {}  
    })  
  }  
  
  CheckVerificationCode(){  
    if(this.UserInputVerificationCode == this.ResponseMessageGetVerificationCodeMessage.code){  
      this.StatusFlagUserVerifyStatus = true  
    }  
  }
}
```

編寫 LoginViewModel

```typescript
import HttpClientHelper from '../../helpers/HttpClientHelper'  
import ServiceContainHelper from '../../helpers/ServiceContainHelper'  
import GetUserInfoMsg from '../../misc/GetUserInfoMsg'  
import UpdateUserInfoMsg from '../../misc/UpdateUserInfoMsg'  
import User from '../models/User'  
import UserInfo from '../models/UserInfo'  

@Observed  
export default class UserInfoPageViewModel{  
  
  /*  
   * status flags   
   * */  
  
  // update user info status  
  statusFlagUpdateUserInfoStatus: boolean = false  
  public get StatusFlagUpdateUserInfoStatus(): boolean {  
    return this.statusFlagUpdateUserInfoStatus;  
  }  
  public set StatusFlagUpdateUserInfoStatus(value:boolean) {  
    this.statusFlagUpdateUserInfoStatus = value  
  }  
  
  /*  
   * request params   
   * */  
  
  // user uuid for user info  
  requestParamUserInfoFunctionParamUserUuid: string = "9b991a40-47db-11ef-ab56-fa163e0d65b3"  
  public get RequestParamUserInfoFunctionParamUserUuid(): string {  
    return this.requestParamUserInfoFunctionParamUserUuid  
  }  
  public set RequestParamUserInfoFunctionParamUserUuid(value: string) {  
    this.requestParamUserInfoFunctionParamUserUuid = value  
  }  
  
  // user uuid for update user info  
  public get RequestParamUpdateUserInfoFunctionParamUserUuid(): string {  
    return this.requestParamUserInfoFunctionParamUserUuid  
  }  
  
  // user name for update user info  
  requestParamUpdateUserInfoFunctionParamUserName: string = ""  
  public get RequestParamUpdateUserInfoFunctionParamUserName(): string {  
    return this.requestParamUpdateUserInfoFunctionParamUserName  
  }  
  public set RequestParamUpdateUserInfoFunctionParamUserName(value: string) {  
    this.requestParamUpdateUserInfoFunctionParamUserName = value  
  }  
  
  // email for update user info  
  requestParamUpdateUserInfoFunctionParamEmail: string = ""  
  public get RequestParamUpdateUserInfoFunctionParamEmail(): string {  
    return this.requestParamUpdateUserInfoFunctionParamEmail  
  }  
  public set RequestParamUpdateUserInfoFunctionParamEmail(value: string) {  
    this.requestParamUpdateUserInfoFunctionParamEmail = value  
  }  
  
  // mobile for update user info  
  requestParamUpdateUserInfoFunctionParamMobile: string = ""  
  public get RequestParamUpdateUserInfoFunctionParamMobile(): string {  
    return this.requestParamUpdateUserInfoFunctionParamMobile  
  }  
  public set RequestParamUpdateUserInfoFunctionParamMobile(value: string) {  
    this.requestParamUpdateUserInfoFunctionParamMobile = value  
  }  
  
  // nice name for update user info  
  requestParamUpdateUserInfoFunctionParamNiceName: string = ""  
  public get RequestParamUpdateUserInfoFunctionParamNiceName(): string {  
    return this.requestParamUpdateUserInfoFunctionParamNiceName  
  }  
  public set RequestParamUpdateUserInfoFunctionParamNiceName(value: string) {  
    this.requestParamUpdateUserInfoFunctionParamNiceName = value  
  }  
  
  // photo for update user info  
  requestParamUpdateUserInfoFunctionParamPhoto: string = ""  
  public get RequestParamUpdateUserInfoFunctionParamPhoto(): string {  
    return this.requestParamUpdateUserInfoFunctionParamPhoto  
  }  
  public set RequestParamUpdateUserInfoFunctionParamPhoto(value: string) {  
    this.requestParamUpdateUserInfoFunctionParamPhoto = value  
  }  
  
  // dvcUuid for update user info  
  requestParamUpdateUserInfoFunctionParamDvcUuid: string = ""  
  public get RequestParamUpdateUserInfoFunctionParamDvcUuid(): string {  
    return this.requestParamUpdateUserInfoFunctionParamDvcUuid  
  }  
  public set RequestParamUpdateUserInfoFunctionParamDvcUuid(value: string) {  
    this.requestParamUpdateUserInfoFunctionParamDvcUuid = value  
  }  
  
  /*  
   * response messages   
   * */  
  
  // get user info request's result  
  // see misc/GetUserInfoMsg for detailed structure  
  responseMessageGetUserInfoMessage: GetUserInfoMsg = new GetUserInfoMsg()  
  public get ResponseMessageGetUserInfoMessage(): GetUserInfoMsg {  
    return this.responseMessageGetUserInfoMessage  
  }  
  public set ResponseMessageGetUserInfoMessage(value: GetUserInfoMsg) {  
    this.responseMessageGetUserInfoMessage = value  
  
    this.userInfo.userName = value.userName  
    this.userInfo.email = value.email  
    this.userInfo.mobile = value.mobile  
    this.userInfo.niceName = value.niceName  
    this.userInfo.photo = value.photo  
    this.userInfo.dvcUuid = value.dvcUuid  
  
    // register instance after get  
    ServiceContainHelper.register("UserInfo", this.userInfo)  
  
    this.RequestParamUpdateUserInfoFunctionParamUserName = value.userName  
    this.RequestParamUpdateUserInfoFunctionParamEmail = value.email  
    this.RequestParamUpdateUserInfoFunctionParamMobile = value.mobile  
    this.RequestParamUpdateUserInfoFunctionParamNiceName = value.niceName  
    this.RequestParamUpdateUserInfoFunctionParamPhoto = value.photo  
    this.RequestParamUpdateUserInfoFunctionParamDvcUuid = value.dvcUuid  
  }  
  
  // update user info request's result  
  // see misc/UpdateUserInfoMsg for detailed structure  
  responseMessageUpdateUserInfoMessage: UpdateUserInfoMsg = new UpdateUserInfoMsg()  
  public get ResponseMessageUpdateUserInfoMessage(): UpdateUserInfoMsg {  
    return this.responseMessageUpdateUserInfoMessage  
  }  
  public set ResponseMessageUpdateUserInfoMessage(value: UpdateUserInfoMsg) {  
    this.responseMessageUpdateUserInfoMessage = value  
    this.StatusFlagUpdateUserInfoStatus = value.success  
    if(!value.success){  
      // update fail  
      this.GetUserInfo()  
    }  
  }  
  
  /*  
   * models   
   * */  
  
  user: User  
  userInfo: UserInfo  
  
  
  constructor() {  
    // di  
    this.user = ServiceContainHelper.resolve<User>("User")  
  
    if(ServiceContainHelper.contain("UserInfo")){  
      this.userInfo = ServiceContainHelper.resolve("UserInfo")  
    }  
    else{  
      this.userInfo = new UserInfo()  
    }  
  
    this.userInfo.userUuid = this.user.userUuid  
    this.RequestParamUserInfoFunctionParamUserUuid = this.user.userUuid  
  
    this.GetUserInfo()  
  }  
  
  
  // get user info  
  async GetUserInfo(){  
    await HttpClientHelper.send({  
      url:"/api/user/queryUserInfoById",  
      extraData:[  
        {name:"userUuid",value:this.RequestParamUserInfoFunctionParamUserUuid}  
      ],  
      success:(data:GetUserInfoMsg) => {  
        console.info("12138--get verify code success: " + data.email + data.mobile + data.niceName + data.userName + data.dvcUuid)  
        // 验证  
        this.ResponseMessageGetUserInfoMessage = data  
      },  
      fail:() => {}  
    })  
  }  
  
  // update user info  
  async UpdateUserInfo(){  
    await HttpClientHelper.send({  
      url:"/api/user/updateUserInfo",  
      extraData:[  
        {name:"userUuid",value:this.RequestParamUpdateUserInfoFunctionParamUserUuid},  
        {name:"userName",value:this.RequestParamUpdateUserInfoFunctionParamUserName},  
        {name:"email",value:this.RequestParamUpdateUserInfoFunctionParamEmail},  
        {name:"mobile",value:this.RequestParamUpdateUserInfoFunctionParamMobile},  
        {name:"niceName",value:this.RequestParamUpdateUserInfoFunctionParamNiceName},  
        {name:"photo",value:this.RequestParamUpdateUserInfoFunctionParamPhoto},  
        {name:"dvcUuid",value:this.RequestParamUpdateUserInfoFunctionParamDvcUuid},  
      ],  
      success:(data:UpdateUserInfoMsg) => {  
        console.info("12138--UpdateUserInfo success: " + data.success + data.msg + data.description)  
        // 验证  
        this.ResponseMessageUpdateUserInfoMessage = data  
      },  
      fail:() => {}    
	})  
  }  
}
```

## 感想與總結

直到現在，我才終於意識到這次實訓應該帶來的影響究竟是什麽

如果只是爲了學會一門語言，或者進步自己的編碼水平，那完全可以在任何項目上完成。被選爲組長后，我依然將目光放在編碼者的角度，思考如何將項目完成的優美且合理。但是這並不是一個處於領導和管理地位的角色應該考慮的事情，管理者的目光始終應該放在項目本身而不是具體實現上，這點直到現在我也不太懂

因此，由於前期項目任務沒有分配好，導致我個人的想法實際上只有自己才清楚，并沒有準確傳達到每一個成員那兒。事實上，僅僅是第二天，就出現了各自爲政，閉門造車的情況。此外，認知上的不平衡也爲合作添加了許多難度，我希望重視結構，但是組内也有追求快速實現的想法，甚至還出現了完全不知道 api 文檔是什麽的個例

通過第一天暴露的問題，我意識到對於一個 team 來説，影響力度大的不是個人能力而是合作的能力。如何將不同的能力、不同的想法和不同的風格組織起來，是一件不易的事情，而在個人開發時不會涉及這方面的問題

於是我學到的第一節課就是，在團隊作業中，要學會的是妥協

我當然覺得自己的設計是最完美的，我有結構，我有層次，我甚至盡可能將其他高級後端語言的特性用這個語言去復現。但是對於其他人來説，在他們的考慮方向下，他們的設計也是最完美的。作爲一個組織者，必須要發揮所有人的能力，不可能一個人逞英雄主義（實際上一開始是打算這麽幹的，但是由於客觀因素被打斷了），這時候就必須對成員妥協，**與其將一個任務分配給能將這個任務做的最好的人，不如將這個任務分配給能做的最好的任務是它的那個人**

得出這種想法后，我考慮重新對項目任務重新分配，於是乎，我現在只需要著手于編寫我的 viewmodel 而不用考慮任何前端 ui 的展示邏輯，但它只是徒有虛名，實際上已經淪爲一個數據提供者了，畢竟開發人員永遠不知道其他人會怎麽去使用。對於個人來説，這是不幸的，但是對於整個項目而言，這樣的妥協卻是繼續推動進度的必要

那麽我應該對於這次實訓抱有何種期待呢？就盡可能體驗完全不同角度下的目光看到的項目該是什麽樣子吧