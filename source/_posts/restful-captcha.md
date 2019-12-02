---
title: Restful风格的验证码
date: 2019-12-02 11:33:10
cateogry: Restful
tags:
    - restful
    - 验证码
    - Spring Boot
---

# Restful风格的验证码

---

<!-- TOC -->

- Restful风格的验证码
- 接口
    - 生成验证码
        - 接口信息
        - 前端显示
    - 校验
        - 接口信息
        - 前端校验
- 移动端使用
    - Android Retrofit Api
    - Android UI
    - 效果展示
- 其他

<!-- /TOC -->

原有的验证码使用流的方式，对移动端不友好，并且现在后端是分布式的微服务系统，原有的基于cookie的验证码方式，显得力不从心。

Restful 风格的验证码，图片使用Base64编码，后端使用Redis存储验证码。Android 客户端使用Retrofit + OkHttp。

# 接口

## 生成验证码

### 接口信息
```bash
curl -X POST \
  http://localhost:8001/captcha/gen \
  -H 'Accept: application/json' \
  -d '{
    "channel": "account_change_pwd",
    "userId": "12345"
}'

{
    "code": 200,
    "msg": null,
    "data": {
        "captchaId": "6593486a-dd27-4e7b-8772-433868555114",
        "imageBase64Header": "data:image/jpeg;base64,",
        "imageBase64": "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"
    }
}
```

```javascript
{
    "channel": "account_change_pwd", // 渠道，一般为模块名称
    "userId": "12345" // 用户唯一编号，区别当前模块的某个用户
}
{
    "code": 200,
    "msg": null,
    "data": {
        "captchaId": "6593486a-dd27-4e7b-8772-433868555114", // 唯一编号
        "imageBase64Header": "data:image/jpeg;base64,", // base64 头部信息
        "imageBase64": "" // 验证码信息
    }
}

```

### 前端显示

前端使用 `imageBase64Header` 与 `imageBase64`即可。

```javascript
function loadImage() {
    $.ajax({
        type: "post",
        url: "/captcha/gen",
        dataType: "json",
        contentType: "application/json",
        data: JSON.stringify({
            channel: "account_change_pwd",
            userId: "12345"
        }),
        success: function (data, status) {
            captchaData = data;
            $('#captcha').attr('src', data.data.imageBase64Header + data.data.imageBase64);
        }
    });
}
```

## 校验

### 接口信息
 
```bash
curl -X POST \
  http://localhost:8001/captcha/check \
  -H 'Accept: application/json' \
  -d '{
    "captchaId": "cb9ec4a1-8a79-41db-a567-b742aa1879a3",
    "captchaText": "fynpf",
    "channel": "account_change_pwd",
    "userId": "12345"
}'

{
    "code": 200,
    "msg": null,
    "data": false
}
```

### 前端校验
```javascript
function check() {
    var text = $('#code').val();
    if (captchaData != null && text.length > 0) {
        $.ajax({
            type: "post",
            url: "/captcha/check",
            dataType: "json",
            contentType: "application/json",
            data: JSON.stringify({
                channel: "account_change_pwd",
                userId: "12345",
                captchaId: captchaData.data.captchaId,
                captchaText: text
            }),
            success: function (data, status) {
                if (data.data) {
                    alert('check success')
                } else {
                    alert('check fail')
                }
            }
        });
    }
}
```

# 移动端使用

## Android Retrofit Api

```kotlin
interface ICaptchaApi {

    @POST("/captcha/gen")
    fun gen(@Body req: CaptchaGenReq): Call<BaseResp<CaptchaGenData>>

    @POST("/captcha/check")
    fun check(@Body req: CaptchaCheckReq): Call<BaseResp<Boolean>>
}
```

## Android UI

```kotlin
class MainActivity : AppCompatActivity() {

    var captchaGenData: CaptchaGenData? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        ivCode.setOnClickListener {
            loadImageCode()
        }
        btnCheck.setOnClickListener {
            check()
        }

        loadImageCode()
    }

    private fun loadImageCode() {
        val req = CaptchaGenReq()
        req.channel = "account_pwd_change"
        req.userId = "12345"

        Apis.captchaApi.gen(req).enqueue(object : Callback<BaseResp<CaptchaGenData>> {

            override fun onResponse(call: Call<BaseResp<CaptchaGenData>>,
                                    response: Response<BaseResp<CaptchaGenData>>) {
                if (response.isSuccessful && response.body() != null) {
                    response.body()?.data?.let {
                        captchaGenData = it

                        val bytes = Base64.decode(it.imageBase64, Base64.DEFAULT)
                        val bitmap = BitmapFactory.decodeByteArray(bytes, 0, bytes.size)
                        ivCode.setImageBitmap(bitmap)
                    }
                }
            }

            override fun onFailure(call: Call<BaseResp<CaptchaGenData>>, t: Throwable) {
            }
        })
    }

    private fun check() {
        val code = edtCode.text.toString()
        if (code.isNotEmpty()) {
            captchaGenData?.let {
                val req = CaptchaCheckReq().apply {
                    captchaId = it.captchaId
                    captchaText = code
                    channel = "account_pwd_change"
                    userId = "12345"
                }

                Apis.captchaApi.check(req).enqueue(object : Callback<BaseResp<Boolean>> {

                    override fun onResponse(call: Call<BaseResp<Boolean>>,
                                            response: Response<BaseResp<Boolean>>) {
                        if (response.isSuccessful) {
                            response.body()?.data?.let {
                                val msg = if (it) "check success " else "check fail"
                                Toast.makeText(this@MainActivity, msg, Toast.LENGTH_LONG).show()
                            }
                        }
                    }

                    override fun onFailure(call: Call<BaseResp<Boolean>>, t: Throwable) {
                    }
                })
            }
        } else {
            Toast.makeText(this, "请输入验证码", Toast.LENGTH_LONG).show()
        }
    }
}
```

## 效果展示

![](https://raw.githubusercontent.com/LiushuiXiaoxia/easy-captcha/master/doc/111.png)

# 其他

[Java 后端](https://github.com/LiushuiXiaoxia/easy-captcha)

[Android 客户端](https://github.com/LiushuiXiaoxia/easy-captcha-android)