
# GTSDK说明文档  
###仅以Xcode工程作示例讲解，若您使用的是其它工程请参考Xcode工程操作，若有不便敬请谅解。  
###Xcode最低版本要求为12.0，SDK最低版本支持9.0。  
###本套SDK仅提供API，无页面。

**最新更新**

    国内SDK优化更新：  
    1、移除登陆和绑定模块的第三方功能facebook、google、gameCenter、apple等（如需相关插件，请自行集成）
    
    2、移除数据上报和数据收集的第三方相关功能firebase、facebook（保留adjust）
    
    3、付费功能移除内购付费、增加h5付费功能（接入方需在提审时候，对付费相关功能和控件进行隐藏）
    
    4、移除订单查询、本地商品信息获取（国内付费模块只需支持中文版、第三方付费不提供客户端订单结果查询功能）  
    
    
 **集成说明**  
 
 ***说明：国内马甲包类的SDK是经过混淆的，原SDK名为GTSDK，header名为GT.h，混淆后”GTSDK“和”GT“皆变，需要新老客户进行进行替换，如：`[GT gt_login]`替换为`[XXX gt_login]`，”XXX“为混淆后的头文件名。文档中API说明，按照原SDK描述。***
 
 1、如果您是首次接入用户，请按照下面步骤进行接入，由于国内SDK做了混淆，相关库和类名可参考demo。  
 
 2、如果您不是首次接入goat，请仔细阅读最近更新中内容，移除的相关模块需要清理（除adjust外的所有第三方功能），相关api如果有调用，需要进行移除。  
 
    用户模块：去掉fb、google、gameCenter相关UI和逻辑。  
    付费模块：SDK仅保留下单接口。国内马甲包请清理掉内购相关配置 和 提审前隐藏商城购买模块（无版号不允许存在付费功能）  
    接口和头文件引入：替换GT、GTSDK等相关字段和头文件（具体内容请针对混淆SDK的名称和类），调用方法都无需更换。
 
 **集成SDK和相关配置**
 
 SDK集成步骤不全编译容易造成较多报错，请将所有步骤完成后再进行编译; 
 
 已经集成旧版SDK的，请移除掉 admob、facebook、firebase等相关功能和文件    
 请移除掉内购相关支持设置  
 
1、导入SDK相关文件包含如下库文件和相关配置文件  
  GTSDK.framework (此sdk为goat基础类库，常混淆类名为其他， 此GTSDK为代称)  
  AdjustSDK.framework  
  AdjustSigSDK.framework (Embed & Sign)  
  AdjustSdkManager.framework  
  
  GoatEvent-Info.json  
  GoatService-Info.plist  
  GoatSdk_Version.json  
  SVProgressHUD.bundle  
  
2、 在TARGETS -> General -> Frameworks,Libraries,and Embedded Content栏目下添加以下framework：

   `accelerate.framework`
   
   `CoreTelephony.framework`
   
   `StoreKit.framework`
   
   `iAd.framework`
   
   `AdSupport.framework`
   
   `AppTrackingTransoarency.framework`
   
   `libc++.tbd`
   
   备注：并检查高图相关库的签名，只有AdjustSigSDK为动态库Embed & Sign ， 其他设置 Do not Embed  
   
  3、 确认除了`AdjustSigSdk.framework`之外其他库的设置为`Do not Embed`。  
	`PROJECT` → `Build Settings`选项卡。选择All,搜索“Other Linker Flags”。将`Other Linker Flags`构建为`-ObjC`  
	`TARGETS` → `Bulid Setting`选项卡,选择All,搜索Bitcode,设置`Enable Bitcode`为 NO。  
	`TARGETS` → `Build Phases`选项卡,选择 `Copy Bundle Resources`,删除`upload-symbols`。  
	
  4、添加钥匙串共享功能：  
  
  	开启Keychain Sharing并设置Keychain Groups的关键字段com.goat.gtKeyChainGroup  
	
  5、添加相关权限说明：
  	在项目的info.plist中添加允许请求http，相机、相册、获取IDFA权限说明(说明描述请使用中文并按照项目需要进行面熟，以下描述仅供参考)：
	
	
	<key>NSAppTransportSecurity</key>
	<dict>
		<key>NSAllowsArbitraryLoads</key>
		<true/>
		<key>NSAllowsArbitraryLoadsForMedia</key>
		<true/>
		<key>NSAllowsArbitraryLoadsInWebContent</key>
		<true/>
	</dict>
	
	
	<key>NSCameraUsageDescription</key>
	<string> “Goat Guides”需要访问相机，以便您可以将照片提交给客户服务</string>
	<key>NSPhotoLibraryAddUsageDescription</key>
	<string>“Goat Guides”需要访问相册，以便您可以将照片提交给客户服务</string>
	<key>NSPhotoLibraryUsageDescription</key>
	<string>“Goat Guides”需要访问相册，以便您可以将照片提交给客户服务</string>
	<key>NSUserTrackingUsageDescription</key>
	<string>您的数据将用于向您投放个性化广告。</string>
	
     
6. 安装证书和描述文件 ，并编译工程。  

**测试**  
1. 在SDK初始化接口之前使用测试专用接口`[GT gt_setAdjLogLevel:1];`；

2. 将gt_production 设置为NO，切换为测试服；
3. 卸载重装运行后在日志栏搜索`iad3`或`apple_ads`,如出现结果即表示功能正常；
4. 注释或移除测试接口`[GT gt_setAdjLogLevel:1];`，恢复正式服。

###变更内容
ATT授权弹框修改为服务器接口控制。
###更新方式

**集成SDK相关API**  
    ***国内用户只需正确接入SDK初始化、用户登陆相关模块、付费模块等必要模块***  
    ***基础库由于被混淆，名字不固定为GTSDK ，相关类名不固定为GT***  
    
    
 一、请求数据跟踪权限弹窗

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
   
    
3 获取数据跟踪授权状态

     int ATTStatus = [GT gt_getIDFAStatus];

    

二、使用
###3.1 接口通用  

```
errCode  返回值
0. GT_SERVER_SUCCESS   请求成功
1. GT_SERVER_FAILURE   请求失败
2. GT_SERVER_CONNECT_FAILED   服务器无法访问
```
<font size=3 color="#dd0000">SDK返回的数据格式为NSDictionary，为了数据正常，请使用NSDictionary接收。</font></br>


###3.2 初始化  
1. 导入头文件`#import <GTSDK/GT.h>`(这里具体名称需按照给定的SDK名称和头文件名)
2. 在didFinishLaunchingWithOptions方法中进行项目的初始化。

 ```
[GT gt_production:YES
application:application didFinishLaunchingWithOptions:launchOptions];
 ```
<font size=3 color="#dd0000">配置开发模式(YES为生产模式，NO为开发模式)，上线之前切记更改为生产模式。</font></br>

###3.3 用户  

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
    GTLoginTypeCommon,

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


###3.4 储值  
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


###3.5 事件上报  

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
 
 
###3.6 客服  

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

###3.7 用户协议<font size=3 color="#dd0000">（按需接入）</font>

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
###3.8 Facebook分享 （国内SDK暂不支持fb分享，因此接口暂不接入）  
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
 
###3.9 通知（按需接入）

 APNS远程推送
 . 在项目中开启Push Notifications权限 ，项目target控制板中”Signing&Capabilities“ 点击 ”+Capability“ 并添加 Push Notifications。

   在Appdelegate.h中设置代理标签<GTNotificationDelegate>

   在Appdelegate.m中项目初始化之后设置代理

 	```
	  [GT sharedInstance].delegate = self;
 	```
	 ```
	- (void)gt_didReceiveRemoteNotification:(NSDictionary *)userInfo{
	    NSLog(@"Recevied");
	}
	 ```

 
###3.10 商店评分（按需接入）

 @param appId 应用在商店的id（可选）
 @param controller 当前视图的控制器
	```
	NSMutableDictionary *params = [NSMutableDictionary dictionary];
	[params gt_setupEvaluationParamsAppID:@"" viewController:self];
	[GT gt_evaluate:params];
	```

###3.11 游戏公告（按需接入）

返回游戏公告的内容

@param completionHandler 完成之后的回调
	```
	[GT gt_announcementCompletionHandler:^(int code, id  _Nullable response) {

	}];
	```


###3.12 通用日志上报<font size=3 color="#dd0000">（按需接入）</font>

 通用日志上报
 
 @param key 记录上传信息的键，可用于统计加载统计等
	```
	[GT gt_reportSdkEventWithKey:@"key"];
	```

###3.13 记录最近一次登陆的游戏信息<font size=3 color="#dd0000">（按需接入）</font>

 @param info 游戏内容参数，参数任意字符串，SDK侧不限制其内容
 @param completionHandler 完成之后的回调

    ```
    NSMutableDictionary *params = [NSMutableDictionary dictionary];
    [params gt_setupUploadGameInfo:@"gameInfo" completionHandler:^(int code, id  _Nullable response) {
    
    }];  
    [GT gt_uploadGameInfo:params];
    ```


###3.14 其他API<font size=3 color="#dd0000">（按需接入）</font>

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


**出包**

由于项目提审不支持模拟器，如果您在提审包的时候有相关报错，那么，打包时需要针对 i386 和 x86_64 架构剥离二进制:
    
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


**故障排除**

####5.1. "image not found"错误 如果您遇到如下所示的错误：


    dyld: Library not loaded: @rpath/GTSDK.framework/GTSDK
      Referenced from: /var/containers/Bundle/Application/5A7F1CE0-E076-4EE4-A4E3-BE9A1EEC7659/Goat Guides.app/GTSDK Guides
      Reason: image not found
                  
报错的内容中包含的库属于动态框架，需要添加到Embedded Binaries：

  1. 在项目导航器中选择您的项目。
  2. 在主视图的左侧选择您的目标。
  3. 转到General 选项卡，选择Embedded Binaries 点击 ​+​ 按钮​，xcode11之后 需要在”Framework,Libraries,andEmbedded Content“中找到对应的库，在库后面的Embed中切换为”Embed&Sign“。 
  4. 清理和重建。

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

####5.4. dyld: Library not loaded: @rpath/libswiftCore.dylib

1. 这是由于sdk版本内包含了swift的内容，Xcode12以下版本需要进行兼容。
2. 将Build Settings > Always Embed Swift Standard Libraries设置为YES。
3. 这一栏的内容原名为Embedded Content Contains Swift Code。


####5.5. -1000 A server with the specified hostname could not be found

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



   
