### 1.后端服务地址

**无间盾服务端访问地址如下：**

* 公网SAAS地址：https://infernal.zhongan.io
* 众安内网地址：http://za-token-verify.pub.za-tech.net

下列内容中API均为上述地址加上对应URL
无间盾服务不提供测试环境, 开发，测试，预发等不同环境可通过SaaS控制台（https://infernal-wall.zhongan.io ）创建不同应用来区分

### 2.JAVA SDK接入（目前仅适用于众安集团）

**SDK的maven依赖添加**

```
<dependency>
   <groupId>com.zhongan.sdk</groupId>
   <artifactId>infernalwall</artifactId>
   <version>1.1.0</version>
</dependency>
```

**输入参数说明**

| 名称 | 类型 | 必须 | 描述 |
|:---|:---|:---|:---|
| url     | string  | 是 | 服务接口地址 |
| appName  | string  | 是 | 申请接入反欺诈服务的业务系统名 |
| did  | string  | 是  | 设备指纹 |
| token  | string  | 是  | 会话token是由前端js或sdk自动生成，非生成签名用的token |
| sceneId  | string  | 是  | 场景id |
| secret  | string  | 是  | 密钥 |
| phone  | string  | 否  | 手机号码,非必填 |
| clientIp  | string  | 否  | 客户端IP，非必填 |

**详细说明**

1）url：

* 众安内部应用值为： http://za-token-verify.pub.za-tech.net/api/v1/server_verify

* 外部用户应用的值为：https://infernal.zhongan.io/api/v1/server_verif

2）appName
* app标识，控制后台中的APPID，该参数值位置见下图：
  ![](/images/appconfig-1.png)

3）sceneId
* 场景ID，该参数具体位置见下图：
  ![](/images/appconfig-3.png)

4）secret
* 接口密钥，该参数具体位置见下图：
  ![](/images/appconfig-2.png)
  
5）clientIp
* 客户端IP，非必填，填入后有助于提高风控能力，准确获取客户端IP可参见文档：https://www.cnblogs.com/xiaoxing/p/6565573.html
  
6）did
* 设备指纹，由前端SDK获取后提交到后台，具体见各前端SDK文档

7）token
* 会话token，由前端SDK获取后提交到后台，具体见各前端SDK文档

**返回参数说明**

ResponseResult:
```
{
        "success": true|false,  ----true表示放行, false:表示拦截
        "message": "{\"msg\": \"antibot result not found\", \"antibot\": null, \"code\": 404, \"result\": false}",  -----返回相关信息
        "code": null,
        "model": null
  }
```
JAVA SDK内置了服务降级措施，当服务超时或者5**错误时结果为true，不影响正常业务

**示例代码**
```
import com.zhongan.sdk.infernalwall.ServerVerify;
    import com.zhongan.sdk.infernalwall.utils.ResponseResult;
    public class demo {
        public static void main(String[] args) {
            boolean isPass = true;
            String appName="c75d58705b32";
            String did = "MmRjY2I3MDkwZDM4YjgyZjNkMDhmZTdlNjI1NTIyNTMtN2Q5YjU3ZTI4ZmVkNGE0MmJlM2FhNDNhMGYyNjA0YTAwMTIyLVhURldRSHhmdThYazB2cEEzUndKL3FMeGdBRT0=";
            String token="2:12:1578969333352:4fda12142808#test#activity::PhDkzM0R:133:000f4f1a06584901e44ba81240ffd5f4ce6dcf29:12";
            String sceneId = "c75d58705b32#prd#account";
            String secret = "9f876e0ddfc29063f7206406";
            String url = "http://za-token-verify.pub.za-tech.net/api/v1/server_verify";
            ServerVerify serverVerify = new ServerVerify();
            ResponseResult ret = serverVerify.check(url,appName,did,token,sceneId,null,null,secret);
            String msg = ret.getMessage();
            if (ret.isSuccess()) {
                System.out.println("放行:" + msg);
            } else {
                System.out.println("拦截：" + msg);
            }

        }
    }
```

### 3.Restfull API 接入

**GET /api/v1/server_verify**

业务方查询客户端风险，处理返回的结果：result=true，说明客户端风险低，正常放行。 result=false说明客户端风险高，对请求行为予以拦截，或者业务方自定义的行为，比如返回用户没有抽到奖。

* 一个客户端token的风险只提供查询一次。多次查询会返回result=false，code=404，防止请求重放的危险。

* 该API接入采用弱依赖，如果后台调用反欺诈接口超时或(HTTP Status code为5XX)错误做bypass处理，直接对请求放行。但是接口返回（HTTP Status）4XX的客户端错误，则必须进行拦截。
* 请求参数:
  
  | 名称 | 类型 | 必须 | 描述 |
  |:---|:---|:---|:---|
  | hashstr     | string  | 是 | 签名：sha1(appName+str(timestamp)+secret).hexdigest()，具体语言实现参考下方示例 |
  | app_name  | string  | 是 | 申请接入反欺诈服务的业务系统ID，参考应用配置管理界面上的APPID |
  | ts  | int  | 是  | 时间戳（秒级）the current time in seconds since the Epoch |
  | token  | string  | 是  | 会话token是由前端js或sdk自动生成，非生成签名用的token |
  | s  | string  | 是  | 场景id，对应应用配置管理界面上的场景ID |
  | secret  | string  | 是  | 密钥 |
  | phone  | string  | 否  | 手机号，有则采集 |
  | clientIp  | string  | 否  | 客户端ip，有则采集 |
  | did  | string  | 是  | 设备指纹 |

其中 secret, app_name, s（即sceneId） 取值参考 输入参数说明

* 响应内容：
 ```
{
    "msg": "antibot result not found",
    "code": 404, // 404：未找到token的风险识别结果，401：签名无效, 200: 正常
    "result": true, // true: human; false: robot
    "antibot": 1 // 人机对抗类型：0：无需人机对抗， 1：滑块，2：图片，3：已被拒绝
}
```
* 如果result＝true，说明该客户端是通过了滑块验证的

* 如果响应内容如下
```
{
  "code": 401,
  "msg": "Requires authentication"
}
```
则代表该api需通过签名授权，签名验证未通过则无法访问，接入反欺诈人际识别服务的应用必须先后台开通并获取到secret，用secret来生成签名来访问该api。secret请参考应用配置管理上的secret。

**生成签名**
* java版生成签名
```
import org.apache.commons.codec.digest.DigestUtils;
long ts = new Date().getTime()/1000;
String hashstr = DigestUtils.sha1Hex(app_name + Long.toString(ts) + secret);
```

* python版生成签名
```
import hashlib, time
ts = int(time.time())
hashstr = hashlib.sha1(app_name + ts + secret).hexdigest()
```

* 生成签名测试
```
app_name=iyb-web
ts=1480905779(这里是秒数，如果你的timestamp是这个的10^3数量级，说明timestamp拿到了毫秒)
secret=47094dac-b798-11e6-9415-00163e0002a7
用上面三个参数代入你的签名算法，如果生成的hashstr不等于52c8bfcb2c02ca85cfed7d2d0b08bfae61bbc38b，
说明生成的签名算法存在问题。
```