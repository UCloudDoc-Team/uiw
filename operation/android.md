### 1. 下载SDK

下载SDK地址如下：[无间盾-Adroid-sdk-1.0.0.zip](https://uiw.cn-gd.ufileos.com/无间盾-Android-sdk-1.0.0.zip)

### 2. 接入SDK

* 该SDK内含示例代码，下载解压后，实际的sdk在app/lib 中，拷贝到自己项目的lib中
* 加入下面的依赖：
```
repositories {
    flatDir {
        dirs 'libs'
    }
}
```
* 根据sdk具体的版本号（X.X.X为版本号，请替换为具体版本号）：
```
repositories {
    flatDir {
        dirs 'libs'
    }
}
```
* Sync Project

### 3. 调用示例
**1.首先调用初始化方法：**
```
WaterProofHelper.getInstance().init(String domain,String key);
```
参数说明如下：
* domain ，是服务的域名，SaaS版服务的值是：https://security.zhongan.io
*  key，数据通信加密密钥, 通过secret 计算得出，key=sha256(secret),secret值见下图
   
![](/images/appconfig-2.png)

**2.其它可选配置**
* 设置国际化语言，不设置默认为简体中文
```
WaterProofHelper.getInstance().setLanguage(String language);
```
* 其中，可选的语言编码：
 ```
 zh -> 中文
 zh-TW -> 中文（繁体）
 en -> 英文
 fr-FR -> 法语
 de-DE -> 德语
 ja -> 日文
```

* 自定义加载中的文案
 ```
 WaterProofHelper.getInstance().setLoadingDes(String des);
```

* 自定义尝试次数过多的的文案
 ```
 WaterProofHelper.getInstance().setTryTooMuchDes(String des);
```

* 自定义网络超时的文案的文案，不设置有默认文案
 ```
WaterProofHelper.getInstance().setNetworkTimeoutDes(String des);
```

**3.开始风险验证**
```
 WaterProofHelper.getInstance().startValidate(Context context,  String appName, String environment， String scenes,  CallBackResultListener listener);
```
入参介绍：
* appName：app标识，控制后台中的APPID，该参数值位置见下图：

![](/images/appconfig-1.png)

* environment: 环境，类似 prd，test，可自定义，不能为空, 目前统一填 prd

* scenes: 场景ID中的场景名称，该参数位置见下图红色框内的字符串

![](/images/appconfig-4.png)
例如截图的 scenes 值为 account

**4.回调参数介绍**

返回的是一个CallBackData，里面包含三个参数：code，token，did

* code：返回的结果描述 ，类型为 int
```
 100 ：直接成功，不需要启动人机对抗，可直接请求业务
 101：直接失败，此用户是黑名单用户，不建议启动后续业务，或自 行处理
 102 ： 人机对抗成功，建议请求自己的后台再次验证，验证成功才 启动后续的业务
 103 ：人机对抗失败，需要重新验证
 104 ：服务异常（可先确认下初始化的domain是否正确，appName是否申请注册过），业务调用方自行决定是否继续后续业务，或者提示用户
 105 ：入参为空，请检查入参
```
* token：会话ID，验证成功后需提交到后台进行校验
* did： 设备指纹，验证成功后需提交到后台进行校验

**5.最终处理逻辑**
* 如果code值为100，则把回调得到的 token，did 联合业务数据提交到后台，后台进行最后校验，后台校验接口文档见： [服务端接入说明](/uiw/operation/backend)
* 如果code值为 101， 102 ， 103 建议重新验证
* 如果code 值为 104 ，服务异常，先确认下初始化的domain是否正确，appName是否申请注册过），业务调用方自行决定是否继续后续业务，或者提示用户
* 如果code 值为105 ，入参为空，请检查入参