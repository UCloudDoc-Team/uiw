### 1. 下载SDK

下载SDK地址如下：[ZASuperValidateSDK_1.0.0.tar.gz](https://uiw.cn-gd.ufileos.com/ZASuperValidateSDK_1.0.0.tar.gz)

### 2. 接入SDK

**1.将Demo中文件名为ZASuperValidateSDK的文件夹添加到你所建的工程中。**

* 备注：ZASuperValidateSDK文件夹包含两个文件：ZASuperValidate.framework和ZASuperSource.bundle

**2.确保“工程-General-Linked Frameworks and Libraries” 中存在ZASuperValidate.framework文件。(如下图)**

![](/images/ios_project.png)

**3.在需要使用SDK的类中导入SDK头文件**

* 确认以上两步完全正确后，在需要使用到SDK的类中导入头文件 *
```
 #import <ZASuperValidate/ZASuperValidateHeaders.h>
```
**4.调用头文件 ZASuperValidateHeaders.h 中的如下方法**
```
/**
 *  启动众安超级验证码人机识别模块
 *  @param appName      申请接入反欺诈服务的应用名：一定要是注册过的应用名
 *  @param environment   环境：可以使用统一的值prd，也可以分环境设置不同的值
 *  @param scene              场景名：业务方可自定义
 *  @param key          申请接入安全验证服务的密匙：一定要是申请过的密匙
 *  @param type          申请接入安全验证服务的密匙：一定要是申请过的密匙
 *  @param completionBlock  结果回调
 */

- (void)startSuperValidateWithDomain:(NSString *)serviceDomain withAppName:(NSString*)appName withEnvironment:(NSString *)environment withScene:(NSString*)scene withKey:(NSString*)key withType:(ZASuperValidateType)type withCallBackBlock:(ZASuperValidateResultBlock)completionBlock;
```
* 示例：
```
 [[ZASuperValidateHeaders sharedInstance] startSuperValidateWithDomain:@"https://vpc-af.zhongan.io" withAppName:@"demoapp" withEnvironment:@"prd" withScene:@"login" withKey:@"f180eb1071f43a2b8c79****************7c9585287955dcd08d83f1" withType:ZASuperValidateTypeAuto withCallBackBlock:^(NSDictionary *callBackDic) {
    //回调参数callBackDic(code:返回码  did:设备指纹 token:会话token)

 }];
```

**5.传入参数说明：**

 startSuperValidateWithDomain
* SaaS服务后端地址，通常为 https://infernal.zhongan.io，如果私有化部署，则为私有化部署后的地址

AppName
* app标识，控制后台中的APPID，该参数值位置见下图：
  
  ![](/images/appconfig-1.png)

Environment
* 环境标识，此处默认填prd即可

Scene
* 场景标识，场景ID中的场景名称，该参数位置见下图红色框内的字符串
  
  ![](/images/appconfig-4.png)

Key
* 数据加密传输密钥，通过secret 计算得出，key=sha256(secret),secret值见下图

![](/images/appconfig-2.png)

type
* 人际对抗类型，选项有：
```
 typedef NS_ENUM(NSInteger, ZASuperValidateType)  {
  ZASuperValidateTypeAuto = 0, //人机对抗类型：由后端自动判定
  ZASuperValidateTypeSlider, //人机对抗类型：滑块
  ZASuperValidateTypeClick, //人机对抗类型：点选
};
```
code
* 返回码，根据返回码判断人机识别结果
```
typedef NS_ENUM(NSInteger, ZASuperValidateCode) {
      ZASuperValidateCode100 = 100, //直接成功，不需要启动人机对抗，直接请求业务
      ZASuperValidateCode101, //直接失败，不启动业务，比如登陆注册领取鼓励金这种业务
      ZASuperValidateCode102, //人机对抗成功，需要请求自己的后台再次验证，验证成功才能启动后续的业务
      ZASuperValidateCode103,    //人机对抗失败，需要重新验证
      ZASuperValidateCode104, //后台数据异常，业务调用方自行决定是否继续后续业务
      ZASuperValidateCode105, //入参格式不对
  };
```
did
* 设备指纹，验证成功后需提交到后台进行校验

token
* 会话ID，验证成功后需提交到后台进行校验

### 3. 其它可选配置

**其它可选配置见示例**

```
 /**
 *  自定义加载过程提示 (注意：调用startSuperValidateWithDomain方法之前设置生效)
 *  @param loadingText  加载过程提示 不设置默认为：加载中…
 */
- (void)setLoadingText:(NSString *)loadingText;
/**
 *  自定义网络错误提示 (注意：调用startSuperValidateWithDomain方法之前设置生效)
 *  @param networkErrorText  网络错误提示 不设置默认为：网络超时，请检查你的网络设置
 */
- (void)setNetworkErrorText:(NSString *)networkErrorText;
/**
 *  自定义尝试过多提示 (注意：调用startSuperValidateWithDomain方法之前设置生效)
 *  @param triedText  尝试过多提示 不设置默认为：尝试过多，请稍后重试
 */
- (void)setTriedText:(NSString *)triedText;
/**
 *  自定义语种 (注意：调用startSuperValidateWithDomain方法之前设置生效)
 *  @param languageType 语种：zh 中文(简体)；zh-TW中文(繁体)；en英文；ja日语；fr-FR法语；de-DE德语
 *  不设置默认为：zh 中文(简体)
 */
- (void)setLanguageType:(NSString *)languageType;
```

**其它方法**
```
 /**
 *  是否开启DEBUG模式 (注意：调用startSuperValidateWithDomain方法之前设置生效)
 *  @param isDebug  是否开启DEBUG模式，默认不开启
 */
- (void)setDebugMode:(BOOL)isDebug;

/**
 *  移除众安超级验证码人机识别模块
 */
- (void)removeSuperAuthCode;
```

