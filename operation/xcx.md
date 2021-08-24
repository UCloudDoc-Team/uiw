# 微信小程序接入文档

### 1. app.json引入插件 ###

```
{
  "plugins": {
    "myPlugin": {
      "version": "1.1.4",
      "provider": "wx06e7b082c4c16e4c"
    }
  }
}
```

### 2. index.json ###

```
{
  "usingComponents": {
    "infernalWall": "plugin://myPlugin/infernalWall"
  }
}
```
### 3. index.wxml ###

```
<button bind:tap="showCode">验证码</button>
<infernalWall
  bind:close="onClose" // 关闭验证码
  visible="{{visible}}" // 验证码显示状态
  s="{{s}}" // 场景ID，从管理后台获取
  userName="{{userName}}" // 当前用户的名字
/>
```

### 4. index.js ###

```
Page({
  onLoad: function () {
    plugin.init({ onSuccess: this.onSuccess }); // 回调函数包括onSuccess、onFail、onFinaly
  },
  data: {
    userName: userName, // 当前用户的用户名
    visible: false,
    s: 'test',
  },
  onClose: function() {
    this.setData({
      visible: false,
    });
  },
  showCode: function(e) {
    this.setData({
      type: e.currentTarget.dataset.type,
      visible: true,
    });
  },
  onSuccess: (data) => {
    console.log('onSuccess -->', data);
  },
})
```

* 组件接受参数：

  | 参数 | 说明 | 类型 | 是否必填 | 默认值 | 版本 |
    |:---|:---|:---|:---|:---|:---|
  | bing:close | 关闭验证码回调  | Function | 是 |  |  |
  | visible  | 验证码是否显示 | Boolean | 是 | | |
  | s | 场景ID，从管理后台获取 | String  | 是 | |  |
  | userName  | 当前用户名  | String  | 是 | |  |
  | onSuccess | 验证成功回调函数 | Function(data)  | 否 | |  |
  | onFail  | 验证失败回调函数 | Function(data)  | 否 | |  |
  | onFinaly  | 无论验证成功或者失败都会执行的函数 | Function(data)  | 否 | | |
  | type | 验证码类型：包含点选(text)、滑块(drag)、不传时后台根据账号安全度选用合适的验证码（推荐不传）| String | 否 | | 1.0.0- |
  | serverDomain  | 请求验证码的服务端域名，测试环境传(https://infernal-tst.zhongan.io)  | String  | 否 | https://infernal-tst.zhongan.io | 1.0.1 |
  | phone  | 手机号码 | Number  | 否 | | |
  | clientIp  | 客户端IP地址 | String  | 否 | | |
  | lang  | 选择提示语语言，需在后台配置 | String  | 否 | zh | |

* 组件接受参数：

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