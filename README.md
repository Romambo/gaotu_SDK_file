# gaotu_SDK_file
#GTSDK说明文档
#####仅以Xcode工程作示例讲解，若您使用的是其它工程请参考Xcode工程操作，若有不便敬请谅解。
#####Xcode最低版本要求为12.0，SDK最低版本支持9.0。
#####本套SDK仅提供API，无页面。

##最新更新

###<font size=4 color="#dd0000">AdService.framework 集成</font>
####1. 安装
1. 在TARGETS -> General -> Frameworks,Libraries,and Embedded Content栏目下添加以下framework：

   `AdServices.framework`
   
   `CoreTelephony.framework`
   
   `StoreKit.framework`
   
   `iAd.framework`
   
   `AdSupport.framework`
   
1. Build Phases -> Link Binary With Libraries,将`AdServices.framework`设置为`Optional`;

####2. 测试
1. 在SDK初始化接口之前使用测试专用接口`[GT gt_setAdjLogLevel:1];`；

2. 将gt_production 设置为NO，切换为测试服；
3. 卸载重装运行后在日志栏搜索`iad3`或`apple_ads`,如出现结果即表示功能正常；
4. 注释或移除测试接口`[GT gt_setAdjLogLevel:1];`，恢复正式服。

###<font size=4 color="#dd0000">Version 3.4.2</font>

####变更内容
1. 新增原厂订单状态查询接口（可选，2021-8-20移除）

2. Firebase升级至7.6.0
3. AdMob中介插件FBAudienceNetwork升级6.3.0

####更新方式
1. 替换`Goat`,`Firebase`,`AdMob`文件夹；

2. 如需查询订单接口，实现教程3.4.3。

###<font size=4 color="#dd0000">Version 3.4.1</font>

####变更内容
ATT授权弹框修改为服务器接口控制。
####更新方式
1. 替换`GTSDK.framework`与 `GoatSdk_Version.json`文件；

2. 根据文档章节2.6 配置iOS14的适配内容；
3. 苹果的ATT弹框启动时SDK已经调用，CP在首次启动时务必`不要调用`；
4. 根据运营要求，在游戏内的节点调用iOS14的请求弹框，会加载引导页面与苹果的系统ATT页面，调用代码：
   ```
       /*
    0: ATTrackingManagerAuthorizationStatusNotDetermined (授权状态待定)
    1: ATTrackingManagerAuthorizationStatusRestricted (授权状态受限)
    2: ATTrackingManagerAuthorizationStatusDenied(已拒绝)
    3: ATTrackingManagerAuthorizationStatusAuthorized(已授权)
    */
    
    [GT gt_showATTWithChannel:GoatShowATTChannelCP completionHandler:^(NSUInteger status) {
        
    }];
   ```
   注：调用时channel使用`GoatShowATTChannelCP`。

###<font size=4 color="#dd0000">Version 3.4.0</font>
####变更内容
1. Facebook，Adjust，Firebase版本升级；

2. 新增idfa弹窗引导页面`gt_requireIDFAWithSdkView`(章节2.6.2)；
3. 新增idfa授权状态查询接口`gt_getIDFAStatus`(章节2.6.3)；
4. 优化付费点金额币种本地化的接口`gt_getProductsWithSkuIds`(章节3.4.2)。

####更新方式
1. 替换对应Goat，Facebook，Adjust，Firebase文件夹内的文件；

2. Firebase原来的framework本次均变为xcframework，注意将旧包中的Firebase系列的framework删除；
3. 更新和插入以下代码(章节2.5.1)；

   3.1 在最后的` </dict> `元素前，插入以下代码，并将代码中包含的your-facebook-id 和your-app-name修改为提供的id和name。

 ```
<key>CFBundleURLTypes</key> <array> <dict> <key>CFBundleURLSchemes</key> <array> <string>fb{your-facebook-id}</string> </array> </dict> </array> <key>FacebookAppID</key> <string>{your-facebook-id}</string> <key>FacebookDisplayName</key> <string>{your-app-name}</string>
<key>LSApplicationQueriesSchemes</key> <array> <string>fbapi</string> <string>fbapi20130214</string> <string>fbapi20130410</string> <string>fbapi20130702</string> <string>fbapi20131010</string> <string>fbapi20131219</string> <string>fbapi20140410</string> <string>fbapi20140116</string> <string>fbapi20150313</string> <string>fbapi20150629</string> <string>fbapi20160328</string> <string>fbauth</string> <string>fb-messenger-share-api</string> <string>fbauth2</string> <string>fbshareextension</string> </array>
 ```
1. 如果使用了FirebaseCrashlytics，将`upload-symbols`删除；

####错误排查
1. Building for iOS, but the linked and embedded framework 'AdjustSigSdk.framework' was built for iOS + iOS Simulator.

   解决方案：
```
    Open Xcode
    Go to your project's "Build Settings"
    Search for "Validate Workspace"
    Set it to "YES"
```

#SDK集成

##1. 安装
<font size=3 color="#dd0000">
SDK集成步骤不全编译容易造成较多报错，请将所有步骤完成后再进行编译;

如果不需要集成AdMob广告功能或Crash崩溃收集功能，请将对应的文件夹删除。

AdMob ->`AdMob`
Crash ->`Firebase/FirebaseCrashlytics`
</font></br>

1. 将GOAT_LIB文件夹移至 Xcode 项目的根目录并将其添加至所有目标。
       ![](https://doc.goatgames.com/server/../Public/Uploads/2020-11-11/5faba438a763f.png)

1. TARGETS → General → Frameworks,Libraries,and Embedded Content 添加以下AdjustSigSdk.framework并选择 Embed & Sign。

    `AdjustSigSdk.framework`

  继续添加高图SDK插件
 `GTSDK.framework`
 `AdjustSdkManager.framework`
 `FacebookSdkManager.framework`
 `FirebaseSdkManager.framework`
 
 添加系统依赖库
 `iAd.framework`
 `AdSupport.framework`
 `CoreTelephony.framework`
 `Accelerate.framewor`
 `libc++.tbd`
 
 ​确认除了`AdjustSigSdk.framework`之外其他库的设置为`Do not Embed`。
1. ​`PROJECT` → `Build Settings`选项卡。选择All,搜索“Other Linker Flags”。将`Other Linker Flags`构建为`-ObjC`
2. `TARGETS` → `Build Settings`选项卡。选择All,搜索“Other Linker Flags”。将`Other Linker Flags`构建为`$(inherited)`
3. `TARGETS` → `Bulid Setting`选项卡,选择All,搜索Bitcode,设置`Enable Bitcode`为 NO。
4. `TARGETS` → `Build Phases`选项卡,选择 `Copy Bundle Resources`,删除`upload-symbols`。
5. `TARGETS` → `Bulid Setting`,选择All,搜索Swift Language Version,将`Swift Language Version`设置为`Swift 4`。
6. 安装证书和描述文件。



##2. 配置
<font size=3 color="#dd0000">
提示：
为了保障项目的正常运行，除红色字体“按需接入”根据产品需求自由接入外，其他内容请务必保证100%接入并通过验证。</font>

####2.1 Sign In With Apple
使用苹果登陆。
苹果登陆功能需要Xcode 11以上的版本（请勿使用Xcode 11.2）
<font size=3 color="#dd0000">苹果登陆功能需对手机系统版本进行识别判断，仅13.0以上版本可以使用和显示</font></br>
![](https://doc.goatgames.com/server/../Public/Uploads/2020-02-04/5e38f8412f52e.png)
####2.2 IAP
开启In-App Purchase功能。
![](https://doc.goatgames.com/server/../Public/Uploads/2020-02-04/5e38fa737bc83.png)
####2.3 Keychain
开启Keychain Sharing并设置Keychain Groups的关键字段`com.goat.gtKeyChainGroup`。
<font size=3 color="#dd0000">请务必配置此项，否则用户数据将异常。</font></br>
![](https://doc.goatgames.com/server/../Public/Uploads/2020-02-04/5e38f9a67185d.png)
####2.4 Game Center
开启Game Center功能。
![](https://doc.goatgames.com/server/../Public/Uploads/2020-02-04/5e38fd0a0b2f7.png)

####2.5 Facebook
1. 在 Xcode 中，右键点击项目的 Info.plist 文件并选择 Open As（打开方式）->  Source Code（源代码）。

```2. 在最后的` </dict> `元素前，插入以下代码，并将代码中包含的your-facebook-id 和your-app-name修改为提供的id和name。

 ```
<key>CFBundleURLTypes</key> <array> <dict> <key>CFBundleURLSchemes</key> <array> <string>fb{your-facebook-id}</string> </array> </dict> </array> <key>FacebookAppID</key> <string>{your-facebook-id}</string> <key>FacebookDisplayName</key> <string>{your-app-name}</string>
<key>LSApplicationQueriesSchemes</key> <array> <string>fbapi</string> <string>fbapi20130214</string> <string>fbapi20130410</string> <string>fbapi20130702</string> <string>fbapi20131010</string> <string>fbapi20131219</string> <string>fbapi20140410</string> <string>fbapi20140116</string> <string>fbapi20150313</string> <string>fbapi20150629</string> <string>fbapi20160328</string> <string>fbauth</string> <string>fb-messenger-share-api</string> <string>fbauth2</string> <string>fbshareextension</string> </array>
 ```

``` 

####2.6 iOS14 适配
#####2.6.1 配置
<font size=3 color="#dd0000">
适配iOS14则最低版本为Xcode12。
弹框
苹果的idfa弹窗每次安装只弹出一次。如需多次测试请卸载重装。
</font>

1. 在项目的info.plist中添加Key值`Privacy - Tracking Usage Description`,Type为字符串，Value内容:
   ```
1.   Your data will be used to deliver personalized ads to you. 
   ```

1. TARGETS → General → Frameworks,Libraries,and Embedded Content 下添加`AppTrackingTransparency.framework`,并设置为`Do Not Embed`;
2. 在Build Phases中，将`AppTrackingTransparency.framework`设置为`Optional`;

#####2.6.2 请求数据跟踪权限弹窗

1. 苹果的ATT弹框启动时SDK已经调用，cp在启动时务必不要调用；
2. 根据运营要求，在游戏内的节点调用iOS14的请求弹框，会加载引导页面与苹果的系统ATT页面，调用代码：
   ```
       /*
    0: ATTrackingManagerAuthorizationStatusNotDetermined (授权状态待定):还未弹窗供用户选择
    1: ATTrackingManagerAuthorizationStatusRestricted (授权状态受限):手机未打开广告追踪权限，于设置-隐私-追踪设置
    2: ATTrackingManagerAuthorizationStatusDenied(已拒绝):已弹窗用户拒绝授权
    3: ATTrackingManagerAuthorizationStatusAuthorized(已授权):已弹窗用户同意授权
    */
    
    [GT gt_showATTWithChannel:GoatShowATTChannelCP completionHandler:^(NSUInteger status) {
        
    }];
   ```
   注：调用时channel 务必使用`GoatShowATTChannelCP`。
   
1. 效果:
    ![](https://doc.goatgames.com/server/../Public/Uploads/2020-11-11/5fabacc5375a3.png)
    
#####2.6.3 获取数据跟踪授权状态

     int ATTStatus = [GT gt_getIDFAStatus];


    
####2.7 Push Notifications<font size=3 color="#dd0000">（按需接入）</font>

APNS远程推送通知

1. 开启Push Notifications。
![](https://doc.goatgames.com/server/../Public/Uploads/2020-02-04/5e38fa0f0efc7.png)
2. 在Appdelegate.h中设置代理标签<GTNotificationDelegate>
![](https://doc.goatgames.com/server/../Public/Uploads/2019-03-19/5c90cd94a6e18.png)

1. 在Appdelegate.m中项目初始化之后设置代理

 ```
[GT sharedInstance].delegate = self;
 ```
4. 实现代理通知的协议方法

 ```
- (void)gt_didReceiveRemoteNotification:(NSDictionary *)userInfo{
}        
```
    
####2.8 Associated Domains<font size=3 color="#dd0000">（按需接入）</font>

深度链接
开启Associated Domains功能并设置Domains关键字段。
![](https://doc.goatgames.com/server/../Public/Uploads/2020-02-04/5e38fc2783ff5.png)

####2.9 相机相册权限
#####2.9.1 申请相机访问权限：
 
 1. 在Info.plist中添加条目`Privacy - Camera Usage Description`
 
 2. 内容设置为`"应用名" need to access the camera so you can submit photos to customer service.`
 3. `应用名`修改为正确的应用名
 
#####2.9.2 申请相册访问权限：
1. 在Info.plist中添加条目`Privacy - Photo Library Additions Usage Description`

2. 内容设置为`"应用名" need to access the photos so you can submit photos to customer service.`
3. `应用名`修改为正确的应用名

   

##3. 使用
####3.1 接口通用

```
errCode  返回值
0. GT_SERVER_SUCCESS   请求成功
1. GT_SERVER_FAILURE   请求失败
2. GT_SERVER_CONNECT_FAILED   服务器无法访问
```
<font size=3 color="#dd0000">SDK返回的数据格式为NSDictionary，为了数据正常，请使用NSDictionary接收。</font></br>


####3.2 初始化
1. 导入头文件`#import <GTSDK/GT.h>`
2. 在didFinishLaunchingWithOptions方法中进行项目的初始化。

 ```
[GT gt_production:YES
application:application didFinishLaunchingWithOptions:launchOptions];
 ```
<font size=3 color="#dd0000">配置开发模式(YES为生产模式，NO为开发模式)，上线之前切记更改为生产模式。</font></br>

####3.3 用户

 **用户验证流程** 
 ![](https://doc.goatgames.com/server/../Public/Uploads/2019-09-26/5d8c84e694b26.png)

 **用户返回参数统一说明** 
 
|参数名|类型|说明|
|:-----   |:-----|-----|
|sign     |string   |签名，md5（gameKey+userId+timestamp）
|username |string   |用户名
|userId   |string   |用户id
|unionId  |string   |高图平台id
|aliasId  |int      |别字id，用户id的简写
|userType |string   |用户类型 个人页面展示绑定类型显示字段
|nick     |string   |用户昵称，个人页面展示绑定昵称显示字段
|avatar   |string   |用户头像
|email    |string   |用户邮箱
|create	  |bool	   |是否新建用户，ture：新用户；false：旧用户
|reset    |bool	   |是否可以重置密码
|bound    |bool     |是否可以绑定，<font size=3 color="#dd0000">ture：可以绑定账号；false：不可绑定</font></br>
|timestamp|string   |时间戳
|errCode	  |int	   |状态码，0：成功；非0：失败
|message  |string   |返回信息 

```
typedef NS_ENUM(NSUInteger, GTLoginType) {
    GTLoginTypeDefault,
    GTLoginTypeFacebook,
    GTLoginTypeGameCenter,
    GTLoginTypeCommon,
    GTLoginTypeApple,
};
```

#####3.3.1 用户登录
 
 @param loginType 社交登录方式。根据枚举GTLoginType传入相对应的参数
 @param viewController 当前页面的控制器
 @param account 账户(邮箱） 非邮箱登录不传
 @param password 账户密码   非邮箱登录不传
 @param completionHandler 完成之后的回调
 ```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupLoginParamsLoginType:GTLoginTypeDefault viewController:self account:nil password:nil completionHandler:^(int code, id  _Nullable response)  {
         if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
			//重新登录
        }
}];
[GT gt_login:params];
 ```

#####3.3.2 用户绑定
 
 @param bindType 绑定的社交方式。根据枚举GTLoginType传入相对应的参数
 @param viewController 当前的视图控制器
 @param account 账户名（邮箱）非邮箱绑定不传
 @param password 账户密码    非邮箱绑定不传
 @param verificationCode 验证码 非邮箱绑定不传
 @param completionHandler 完成之后的回调
 ```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupAccountBindParamsType:GTLoginTypeCommon viewController:self account:@"" password:@"" verificationCode:@"" completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];
[GT gt_bind:params];
 ```     
 
#####3.3.3 邮箱账户修改密码
 
 @param oldPsw 旧密码
 @param newPsw 新密码
 @param completionHandler 完成之后的回调
 ```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupAccountPasswordAlterParamsOldPaw:@"" newPsw:@"" completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];
[GT gt_alterPassword:params];
 ```
#####3.3.4 邮箱账户找回密码
 
 @param account 账号（邮箱）
 @param password 账户密码
 @param verificationCode 验证码
 @param completionHandler 完成之后的回调 
 ```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupAccountResetPasswordParamsAccount:@"" password:@"" verificationCode:@"" completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];
[GT gt_resetPassword:params];
 ```
#####3.3.5 邮箱账户发送验证码
 
 @param account 登录账号（邮箱）
 @param completionHandler 完成之后的回调
 ```
 NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupAccountSendVerificationCodeParamsAccount:@"" completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];
[GT gt_sendVerificationCode:params];
 ```
 
#####3.3.6 绑定游戏角色

用户创建角色或修改角色名时触发

 @param serverName 服务器名称
 @param serverID 服务器id
 @param roleName 角色名称
 @param roleID 角色id
 @param roleLevel 角色等级
 ```
 NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupBindParamsRoleID:@"roleId" serverName:@"serverName" serverID:@"serverId" roleName:@"roleName" roleLevel:@"roleLevel"];
[GT gt_bindRole:params];
 ```


####3.4 储值
#####3.4.1 储值
 **储值返回参数说明** 
 
|参数名|类型|说明|
|:-----   |:-----|-----|
|errCode	  |int	   |状态码，0：成功；非0：失败
|message  |string   |返回信息 

 苹果内支付。调用苹果支付完成储值功能
 
 @param product 商品付费点id
 @param description 商品描述
 @param amount 商品金额
 @param currency 币种
 @param serverName 服务器名称
 @param serverID 服务器id
 @param roleName 角色名称
 @param roleID 角色id
 @param roleLevel 角色等级
 @param cpOrderId cp订单号
 @param ext       透传参数
 @param completionHandler 完成之后的回调
```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupPayParamsProduct:@"goat.ksrgl.1usd" description:@"60 gems"  amount:@"0.99" currency:@"USD" serverName:@"serverName" serverID:@"serverID" roleName:@"roleName" roleID:@"roleID" roleLevel:@"level" cpOrderId:@"cpid" ext:@"ext" completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];
[GT gt_pay:params];
```

#####3.4.2 获取本地化付费信息<font size=3 color="#dd0000">（按需接入）</font>

|参数名|类型|说明|
|:-----   |:-----|-----|
|skuIds	  |NSSet	   |商品Id的集合

|返回值|类型|说明|
|:-----   |:-----|-----|
|products  |NSDictionary   |本地化商品信息，第一层key为skuId，第二层为商品信息
|mFormatPrice	  |NSString |统一格式化信息 e.g. USD$0.99
|mPrice	  |NSString |本地价格
|mPriceCurrencyCode	  |NSString |本地币种
|mPriceCurrencySymbol	  |NSString |本地货币符号
|mTitle	  |NSString |商品名称
|mDescription	  |NSString |商品描述

```
NSSet *skuIds = [NSSet setWithObjects:@"skuId1",@"skuId2",@"skuId3",@"skuId4",@"skuId5", nil];   

[GT gt_getProductsWithSkuIds:skuIds completionHandler:^(NSDictionary * _Nullable products) {
    
    NSDictionary *sku1 = products[@"skuId1"];
    NSString *price = sku1[mPrice];
    NSString *currency = sku1[mPriceCurrencyCode];
    NSString *formatePrice = sku1[mFormatPrice];
    
    NSDictionary *sku2 = products[@"skuId2"];
    NSString *price2 = sku2[mPrice];
    NSString *currency2 = sku2[mPriceCurrencyCode];
    NSString *formatePrice2 = sku2[mFormatPrice];
    
    //more skuIds...
}];
```

#####3.4.3 查询订单状态<font size=3 color="#dd0000">（按需接入）</font>

|参数名|类型|说明|
|:-----   |:-----|-----|
|cpOrderId	  |NSString	   |原厂订单Id

```
    [GT gt_queryCpOrderId:@"cpOrderId" completionHandler:^(int code, id  _Nullable response) {
        
        if (code == GT_SERVER_SUCCESS) {
            //success
            //订单状态，取值：unpaid，payment，complete, cancel
            NSString *orderStatus = response[@"data"][@"status"];
            if ([orderStatus isEqualToString:@"payment"] || [orderStatus isEqualToString:@"complete"]) {
                //paid
            }
        }
    }];
```

####3.5 事件上报

 配置普通事件上报接口gt_TrackEvent的参数。
 
 @param eventName 上报的事件定义名称
 @param serverName 服务器名称
 @param serverID 服务器id
 @param roleName 角色名称
 @param roleID 角色id
 @param roleLevel 角色等级
 @param remark 扩展参数
 @param channel 上报渠道 
 
`GTThirdAdvertisementNone | GTThirdAdvertisementFacebook | GTThirdAdvertisementFirebase | GTThirdAdvertisementAdjust | GTThirdAdvertisementAllChannel`
 ```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupEventTrackParamsEventName:@"AchieveLevel" serverName:@"serverName" serverID:@"serverID" roleName:@"roleName" roleID:@"roleID" roleLevel:@"level" remark:nil channel:GTThirdAdvertisementAllChannel];
[GT gt_trackEvent:params];
 ```
 
 
####3.6 客服
#####3.6.1 展示客服界面
 
 @param controller 当前视图的控制器
 @param serverName 服务器名称
 @param serverID 服务器id
 @param roleName 角色名称
 @param roleID 角色id
 @param roleLevel 角色等级
```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupCustomerParamsViewController:self serverName:@"S5" serverID:@"50000" roleName:@"Terr framate" roleID:@"32003048018" roleLevel:@"115"];
[GT gt_customer:params];
```

#####3.6.2 显示未读消息小红点<font size=3 color="#dd0000">（按需接入）</font>

```
NSMutableDictionary *params = [NSMutableDictionary dictionary];[params gt_setupAccountUnreadMeaasgeCompletionHandler:^(int code,id  _Nullable response) {
    if (code == GT_SERVER_SUCCESS) {
        NSInteger unreadCount = [response[@"data"][@"total"] integerValue];
        if (unreadCount > 0) {
            //show red point
        }
    }
}];   
[GT gt_getCustomerUnreadMessage:params];
```

####3.7 用户协议<font size=3 color="#dd0000">（按需接入）</font>

 展示用户协议界面
 
 @param controller 当前视图的控制器
 @param serverName 服务器名称
 @param serverID 服务器id
 @param roleName 角色名称
 @param roleID 角色id
 @param roleLevel 角色等级
```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupAgreementParamsViewController:self serverName:@"S5" serverID:@"50000" roleName:@"Terr framate" roleID:@"32003048018" roleLevel:@"115"];
[GT gt_agreement:params];
```
####3.8 Facebook分享<font size=3 color="#dd0000">（按需接入）</font>
#####3.8.1 分享图片和链接

@param content 分享的图片或链接（请使用标准URL链接）
@disussion 照片大小必须小于 12MB
@disussion 用户需要安装版本 7.0 或以上的原生 iOS 版 Facebook 应用
@param viewController 当前的视图控制器 @param completionHandler 完成之后的回调
 ```
NSString *urlString = [NSString stringWithFormat:@"https://www.goatgames.com"];
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupShareParamsContent:urlString viewController:self completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];

UIImage *image = [UIImage imageNamed:@"image"];
[params gt_setupShareParamsContent:image viewController:self completionHandler:^(int code, id  _Nullable response)  {
        if (code == GT_SERVER_SUCCESS) {
            //成功 读取用户数据 eg:
              self.resultLabel.text = response[@"message"];
        }else{
            //失败
        }
}];
[GT gt_share:params];
 ```
 
####3.9 通知<font size=3 color="#dd0000">（按需接入）</font>

 APNS远程推送
 ```
- (void)gt_didReceiveRemoteNotification:(NSDictionary *)userInfo{
    NSLog(@"Recevied");
}
 ```

 
####3.10 商店评分<font size=3 color="#dd0000">（按需接入）</font>

 @param appId 应用在商店的id（可选）
 @param controller 当前视图的控制器
```
NSMutableDictionary *params = [NSMutableDictionary dictionary];
[params gt_setupEvaluationParamsAppID:@"" viewController:self];
[GT gt_evaluate:params];
```

####3.11 游戏公告<font size=3 color="#dd0000">（按需接入）</font>

返回游戏公告的内容

@param completionHandler 完成之后的回调
```
[GT gt_announcementCompletionHandler:^(int code, id  _Nullable response) {

}];
```


####3.12 通用日志上报<font size=3 color="#dd0000">（按需接入）</font>

 通用日志上报
 
 @param key 记录上传信息的键，可用于统计加载统计等
```
[GT gt_reportSdkEventWithKey:@"key"];
```

####3.13 记录最近一次登陆的游戏信息<font size=3 color="#dd0000">（按需接入）</font>

 @param info 游戏内容参数，参数任意字符串，SDK侧不限制其内容
 @param completionHandler 完成之后的回调

    ```
    NSMutableDictionary *params = [NSMutableDictionary dictionary];
    [params gt_setupUploadGameInfo:@"gameInfo" completionHandler:^(int code, id  _Nullable response) {
    
    }];  
    [GT gt_uploadGameInfo:params];
    ```


####3.14 其他API<font size=3 color="#dd0000">（按需接入）</font>

#####3.14.1 获取设备id 
e.g., "7E764935-24CB-4EE3-B718-2BB2C5BA5A0A".
`[GT gt_deviceId]`

#####3.14.2 获取设备名称
 e.g., "iPhone8,1".
 `[GT gt_deviceModel]`
 
#####3.14.3 获取系统版本
e.g., "11.4".
`[GT gt_deviceOSVersion]`

#####3.14.4 获取获取设备类型
目前固定为 "ios".
`[GT gt_deviceType]`

#####3.14.5 获取平台类型
e.g., "AppleStore".
`[GT gt_platform]`

#####3.14.6 获取网络类型
e.g., "WIFI".
`[GT gt_getNetwork]`

#####3.14.7 获取系统设置语言
e.g., "en".
`[GT gt_getSystemLanguage]`

##4. 出包
由于项目使用的动态框架，打包时需要针对 i386 和 x86_64 架构剥离二进制:
    
1. 在项目导航器中选择您的项目。

2. 在主视图的左侧选择您的目标。
3. 转到 ​Build Phases​ 选项卡，点击 ​+​ 按钮并选择 ​New Run Script Phase​。
4. 随即显示新的运行脚本，将其命名为 ‘Stripping Framework’。 
5. 将[​此内容](https://pastebin.com/TWAh5tAW "​此链接​")中的代码片段复制到输入区域。
```
    # This script strips all non-valid architectures from dynamic libraries in
    # the application's `Frameworks` directory.
    #
    # The following environment variables are required:
    #
    # BUILT_PRODUCTS_DIR
    # FRAMEWORKS_FOLDER_PATH
    # VALID_ARCHS
    # EXPANDED_CODE_SIGN_IDENTITY
    
    
    # Signs a framework with the provided identity
    code_sign() {
        # Use the current code_sign_identitiy
        echo "Code Signing $1 with Identity ${EXPANDED_CODE_SIGN_IDENTITY_NAME}"
        echo "/usr/bin/codesign --force --sign ${EXPANDED_CODE_SIGN_IDENTITY} --preserve-metadata=identifier,entitlements $1"
        /usr/bin/codesign --force --sign ${EXPANDED_CODE_SIGN_IDENTITY} --preserve-metadata=identifier,entitlements "$1"
    }
    
    # Set working directory to product’s embedded frameworks 
    cd "${BUILT_PRODUCTS_DIR}/${FRAMEWORKS_FOLDER_PATH}"
    
    echo "Stripping frameworks"
    
    for file in $(find . -type f -perm +111); do
        # Skip non-dynamic libraries
        if ! [[ "$(file "$file")" == *"dynamically linked shared library"* ]]; then
            continue
        fi
        # Get architectures for current file
        archs="$(lipo -info "${file}" | rev | cut -d ':' -f1 | rev)"
        stripped=""
        for arch in $archs; do
            if ! [[ "${VALID_ARCHS}" == *"$arch"* ]]; then
                # Strip non-valid architectures in-place
                lipo -remove "$arch" -output "$file" "$file" || exit 1
                stripped="$stripped $arch"
            fi
        done
        if [[ "$stripped" != "" ]]; then
            echo "Stripped $file of architectures:$stripped"
            if [ "${CODE_SIGNING_REQUIRED}" == "YES" ]; then
                code_sign "${file}"
            fi
        fi
    done
```
6. 清理和重建。

   <font size=3 color="#dd0000">如果集成了FirebaseCrashlytics的崩溃收集框架，则需要更多操作：</font>
1. 转到 ​Build Phases​ 选项卡，点击 Copy Bundle Resources​。
2. 如果目录下包含`upload-symbols`文件则删除该文件。
3. 清理和重建。


##5. 故障排除

####5.1. "image not found"错误 如果您遇到如下所示的错误：


    dyld: Library not loaded: @rpath/GTSDK.framework/GTSDK
      Referenced from: /var/containers/Bundle/Application/5A7F1CE0-E076-4EE4-A4E3-BE9A1EEC7659/Goat Guides.app/GTSDK Guides
      Reason: image not found
                  
您正在使用动态框架，需要添加到Embedded Binaries：

  1. 在项目导航器中选择您的项目。
  2. 在主视图的左侧选择您的目标。
  3. 转到General 选项卡，选择Embedded Binaries 点击 ​+​ 按钮​。
  4. 选择GOAT_LIB文件夹下的GTSDK.framework之后点击Add。 
  5. 清理和重建。

####5.2. "Undefined symbol: _vDSP_mmul/_vDSP_dotpr/_vDSP_vclip"

```
Undefined symbols for architecture arm64:
ld: symbol(s) not found for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```
添加
`Accelerate.framework`
`libc++.tbd`
即可解决

####5.3. "Failed to verify bitcode" 错误 如果您遇到如下所示的错误:

Failed to verify bitcode in AdjustSdk.framework/AdjustSdk:
    error: Cannot extract bundle from
    /var/folders/qy/ylq4b9750lg6x21scz02hq5c0000gn/T/IDEDistributionOptionThinning.Xnl/Payload/AdjustExample-iOS.app/Frameworks/AdjustSdk.framework/AdjustSdk (i386)
    
您正在使用动态框架，需要针对 i386 和 x86_64 架构剥离二进制:
    
1. 在项目导航器中选择您的项目。
2. 在主视图的左侧选择您的目标。
3. 转到 ​Build Phases​ 选项卡，点击 ​+​ 按钮并选择 ​New Run Script Phase​。
4. 随即显示新的运行脚本，将其命名为 ‘Stripping Framework’。 
5. 将[​此链接​](https://pastebin.com/TWAh5tAW "​此链接​")中的代码片段复制到输入区域。
6. 清理和重建。

####5.4. "Terminating app due to uncaught exception"
'InvalidOperationException', reason: 'fb00000000000 is not registered as a URL scheme. Please add it in your Info.plist'

1. FacebookId配置和项目不一致，请确认Bundle Id和Facebook Id是否匹配。
2. 转到Info -> URL Types -> URL Schemes，填充"fb正确的id",例如：`fb22000000000000`。
3. 清理和重建。

####5.5. dyld: Library not loaded: @rpath/libswiftCore.dylib

1. 这是由于sdk版本内包含了swift的内容，Xcode12以下版本需要进行兼容。
2. 将Build Settings > Always Embed Swift Standard Libraries设置为YES。
3. 这一栏的内容原名为Embedded Content Contains Swift Code。

####5.6. Undefined symbols for architecture arm64: method descriptor for Swift.CustomStringConvertible.description.getter : Swift.String"
```
ld: warning: Could not find or use auto-linked library 'swiftCoreGraphics'
ld: warning: Could not find or use auto-linked library 'swiftDarwin'
ld: warning: Could not find or use auto-linked library 'swiftUIKit'
·····
Undefined symbols for architecture arm64:
  "method descriptor for Swift.CustomStringConvertible.description.getter : Swift.String"
```
1. 这是facebook-ios-sdk v7以上要求应用程序使用swift界面，因此项目中需要包含swift文件。
2. 在项目中新建一个空的swift文件即可解决。

####5.7. -1000 A server with the specified hostname could not be found

1. 打开设问题备（iPad/iPod/iPhone）的设置。
2. 点击Wi-Fi，然后点击Wi-Fi名称后面的 "i"。
3. 点开 "配置DNS"，选择 "手动"。
4. 输入1.1.1.1.1和8.8.8.8。
5. 保存。

####5.8 Building for iOS, but the linked and embedded framework 'AdjustSigSdk.framework' was built for iOS + iOS Simulator.

1. Open Xcode
2. Go to your project's "Build Settings"
3. Search for "Validate Workspace"
4. Set it to "YES"

#6. 集成崩溃检测框架<font size=5 color="#dd0000">（按需接入）</font>

<font size=3 color="#dd0000">
提示：
如果游戏项目中已经集成了Cransh工具，例如Bugly，请勿集成本框架，以免相互干扰。</font>

1. 将`Firebase`文件夹倒入导入到项目中。

2. `Target` -> `Build Phases`标签页，找到 Debug Information Format，将其设置为`DWARF with dSYM File`。
3. `Target` -> `Build Phases`标签页，然后依次点击 + > New Run Script Phase。
4. 展开随即显示的新 Run Script 部分。在脚本字段（位于 Shell 字段下）中，添加一个新的运行脚本：

    ```
    "${PROJECT_DIR}/GOAT_LIB/Firebase/FirebaseCrashlytics/run"
    ```
  
  路径为文件的本地路径，确保编译时可以访问到。
    
1. 在构建阶段的 Input Files 字段中提供以下内容：
   注意下列为两条内容，排列顺序见下图
    ```
    $(SRCROOT)/$(BUILT_PRODUCTS_DIR)/$(INFOPLIST_PATH)
    ${DWARF_DSYM_FOLDER_PATH}/${DWARF_DSYM_FILE_NAME}/Contents/Resources/DWARF/${TARGET_NAME}
    ```
      
    ![](https://doc.goatgames.com/server/../Public/Uploads/2020-11-18/5fb41499ebd1c.png)


    
<font size=3 color="#dd0000">注意：
请确保您的该Run Script构建是此项目的最后一个构建阶段，否则 Crashlytics 将无法正确初始化。</font>

    
###可能遇到的问题
```
    Undefined symbols for architecture arm64:
      "vtable for __cxxabiv1::__class_type_info", referenced from:
          typeinfo for std::__1::__basic_string_common<true> in Crashlytics(CLSException.o)
      NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.
      "typeinfo for std::exception", referenced from:
          GCC_except_table1 in Crashlytics(CLSException.o)
          typeinfo for std::exception const* in Crashlytics(CLSException.o)
      NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.
```
    原因:具体错误是因为libc++没有互连。
    
    Crashlytics 3.0需要`libc++.tbd`（不是`libstdc++`）,`libz.tbd`,`SystemConfiguration.framework`和`Security.framework`。
        
    解决方案：在Embedded Content中`libc++.tbd`,`libz.tbd`,`SystemConfiguration.framework`和`Security.framework`。

将 SDK 添加到应用后，Crashlytics 会自动开始侦听并收集崩溃报告。

