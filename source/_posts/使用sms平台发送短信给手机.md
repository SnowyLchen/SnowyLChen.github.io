---
title: 使用SMS平台发送短信给手机
date: 2020-06-05 11:47:23
tags: java
categories: java发送
---

# 1.导入相关的jar

```xml
<!--短信发送开始-->
    <!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
    <dependency>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
      <version>1.1.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/commons-httpclient/commons-httpclient -->
    <dependency>
      <groupId>commons-httpclient</groupId>
      <artifactId>commons-httpclient</artifactId>
      <version>3.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/commons-codec/commons-codec -->
    <dependency>
      <groupId>commons-codec</groupId>
      <artifactId>commons-codec</artifactId>
      <version>1.9</version>
    </dependency>
<!--短信发送结束-->
```



# 2.编写控制器

```java
 	/**
     * 发送短信
     */
    @ResponseBody
    @RequestMapping("/sendText")
    public String send(String online,String phone) throws IOException {
        HttpClient client = new HttpClient();
        PostMethod post = new PostMethod("http://gbk.sms.webchinese.cn");
        // 在头文件中设置转码
        post.addRequestHeader("Content-Type","application/x-www-form-urlencoded;charset=gbk");
        NameValuePair[] data ={
                new NameValuePair("Uid", "你的sms平台用户名"),  // 用户名
                new NameValuePair("Key", "你的sms平台key"),  // Key使用的是密钥，而不是你登录账户的登录密码
                new NameValuePair("smsMob",phone),  // 发送短息具体到手机的手机号码
                new NameValuePair("smsText","【签名信息】"+online)
        };
        post.setRequestBody(data);
        client.executeMethod(post);
        Header[] headers = post.getResponseHeaders();
        int statusCode = post.getStatusCode();
        System.out.println("statusCode:"+statusCode);
        for(Header h : headers){
            System.out.println(h.toString());
        }
        String result = new String(post.getResponseBodyAsString().getBytes("gbk"));
        System.out.println("打印返回消息状态"+result); //打印返回消息状态
        post.releaseConnection();
        return result;
    }
```

# 3.返回参数

| 参数变量     | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| Gbk编码Url   | http://gbk.api.smschinese.cn/                                |
| Utf-8编码Url | http://utf8.api.smschinese.cn/                               |
| Uid          | sms用户名（如您无用户名请先注册）[[免费注册\]](http://sms.webchinese.cn/reg.shtml) |
| Key          | 注册时填写的接口秘钥（可到用户平台修改接口秘钥）[[立刻修改\]](http://sms.webchinese.cn/user/?action=key) 如需要加密参数，请把Key变量名改成KeyMD5， KeyMD5=接口秘钥32位MD5加密，大写。 |
| smsMob       | 目的手机号码（多个手机号请用半角逗号隔开）                   |
| smsText      | 短信内容，最多支持400个字，普通短信70个字/条，长短信64个字/条计费 |

注： 多个手机号请用半角,隔开
如：13888888886,13888888887,1388888888 一次最多对100个手机发送
短信内容支持长短信，最多400字，普通短信70个字/条含签名，长短信64字/条计费 



## 接口返回值

| 短信发送后返回值 | 说　明                                                       |
| ---------------- | ------------------------------------------------------------ |
| -1               | 没有该用户账户                                               |
| -2               | 接口密钥不正确 [[查看密钥\]](http://sms.webchinese.cn/User/?action=key) 不是账户登陆密码 |
| -21              | MD5接口密钥加密不正确                                        |
| -3               | 短信数量不足                                                 |
| -11              | 该用户被禁用                                                 |
| -14              | 短信内容出现非法字符                                         |
| -4               | 手机号格式不正确                                             |
| -41              | 手机号码为空                                                 |
| -42              | 短信内容为空                                                 |
| -51              | 短信签名格式不正确 接口签名格式为：【签名内容】              |
| -52              | 短信签名太长 建议签名10个字符以内                            |
| -6               | IP限制                                                       |
| 大于0            | 短信发送数量                                                 |

