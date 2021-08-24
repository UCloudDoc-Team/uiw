### 方法一：通过npm

**1. 安装npm包**
```
npm install @zhongan/za-infernal-wall --save --registry https://npm.zhonganinfo.com
```

**2. 引入包**
```
import InfernalWall from '@zhongan/za-infernal-wall';
import '@zhongan/za-infernal-wall/index.css'; // 引入css
```
**3. 使用**
```
const infernalWall = new InfernalWall({
  scene: 'test',
  // serverDomain: 'https://infernal-tst.zhongan.io', // 默认生产环境，有需要可切换请求地址
  // phone: '13212345678', // 若开启服务端黑白名单功能，手机号必传
});
infernalWall.show();
```

### 方法二：通过引入 js 文件

**1.引入 css 和 js 文件：**
```
<link rel="stylesheet" href="https://infernal-wall-sdk.zhongan.io/sdk/1.1.1/supercode.css" />
<script type="text/javascript" src="https://infernal-wall-sdk.zhongan.io/sdk/1.1.1/supercode.js"></script>
```
**2. 实例验证码对象**
```
<script>
    var code = new SuperCode({
        scene: 'activity#prd#login',
        // serverDomain: 'https://infernal-tst.zhongan.io', // 默认生产环境，有需要可切换请求地址
        // phone: '13212345678', // 若开启服务端黑白名单功能，手机号必传
    });
</script>
```

**3. 显示验证码**
```
code.show(); // 1.0.1版本之后可以传入参数，参数同 new SuperCode() 时
```
**4. 接口校验**

校验成功时，会触发onSuccess回调函数，onSuccess参数见下面 回调函数的参数，用data中的数据与后台校验。

* 实例对象时传入参数：
  
  | 参数 | 说明 | 类型 | 是否必填 | 默认值 | 版本 |
  |:---|:---|:---|:---|:---|:---|
  | type     | 验证码类型：包含点选(text)、滑块(drag)、不传时后台根据账号安全度选用合适的验证码，（推荐不传）  | String | 否 |  |  |
  | placement  | 验证码在按钮的哪个方位: left、right。仅PC端  | String | 否 | right | |
  | placementRange  | 验证码距离按钮边界的距离，值范围[0-1]  | Number  | 否 | 0.2 |  |
  | serverDomain  | 调用接口的域名	  | String  | 否 | 根据环境 |  |
  | lang  | 选择提示语语言，需在后台配置  | String  | 否 | zh |  |
  | onSuccess  | 验证成功回调函数  | Function(data)  | 否 |  |  |
  | onFail  | 验证失败回调函数 | Function(data)  | 否 | |  |
  | onFinaly  | 无论验证成功或者失败都会执行的函数  | Function(data)  | 否 | | |
  | getPopupContainer  | 验证码渲染父节点。默认渲染到 body 上，如果遇到验证码滚动定位问题，试试修改为滚动的区域，并相对其定位  | Function  | 否 | () => document.body | |
  | getButtonNode  | 验证码定位的按钮节点。仅PC端  | Function  | 否 | () => document.body | |
  | textStyle  | 点选验证码生成的数字的样式  | Object  | 否 | | |
  | errorMessageStyle  | 错误消息样式	  | Object  | 否 | | |
  | scene  | 场景ID，从管理后台获取 | String  | 否 | | |
  | phone  | 手机号码 | String  | 否 | | |
  | clientIp  | 客户端IP地址 | String  | 否 | | |
  | tooltip  | 是否显示提示信息 | Boolean  | 否 | false | 1.0.2 |
  | timeout  | timeout时间 | number  | 否 | 3000 | 1.1.1 |

* 可选语言（lang）
```
 zh -> 中文
 zh-TW -> 中文（繁体）
 en -> 英文
 fr-FR -> 法语
 de-DE -> 德语
 ja -> 日文
```

* 回调函数的参数data:
```
 {
  code: 0,
  message: '验证成功',
  data: {
    did: 'xxx',
    token: 'xxx',
    scene: 'xxx',
  }
}
```
* code列表:
  
  | code | 结果 | message |
  |:---|:---|:---|
  | 0   |  success | 验证成功 |
  | 10  | fail | 尝试过多，请稍后再试 |
  | 11  | fail | 账号异常，请稍后再试 |
  | 12  | fail | 网络错误，请检查网络连接再重试 |
  | 13  | fail | 用户取消操作 |

* 实例对象的静态方法：

| 方法名	 | 描述 | 版本 |
  |:---|:---|:---|
| show   |  显示验证码。版本1.0.1之后可以传入参数，参数类型同实例时参数，会合并实例参数 |  |
| hide  | 隐藏验证码	 | |
