# API Document

这是中国海洋大学 2020 年软件工程课程设计 iBird 项目的接口文档。

## 总体

以下为对所有接口的总体概述

### 域名

域名为 https://weparallelines.top

**注意：** 使用 HTTPS

### 接口格式

接口的请求格式一般为 JSON 格式，少数为 MultiForm 格式。

接口的响应格式为 JSON 格式。

### 接口访问频率

接口访问频率有 3 级限制

- 严格：2 秒 2 次
- 中等：1 秒 2 次
- 宽松：1 秒 5 次

超过访问频率会返回 50403 "Forbidden"。

### 响应字段

响应字段必定包含**状态码 code** 和**状态信息 msg**。

其他额外字段均包含在**数据 data** 字段中。

**注意：** 以下所有接口的响应字段除 code 和 msg 字段均在 data 字段中，为了书写文档简便其他字段与 code 和 msg 字段并列。

### 响应状态码和状态信息

响应状态码 code 和状态信息 msg 一一对应，无重复。

未详尽的状态码和状态信息参考 https://github.com/OUC-iBird/iBird_Back_End/blob/main/apps/utils/response_status.py

## 接口

### 上传图片

**接口功能**

> 上传图片

**URL**

> /api/upload

**访问频率限制**

> 严格：2 秒 2 次

**请求方式**

> POST

**请求格式**

> MultiForm

**请求字段**

> | 参数  | 必选 | 类型   | 说明                                 |
> | :---- | :--- | :----- | ------------------------------------ |
> | img   | true | file   | 图片文件（jpg、png）                 |
> | usage | true | string | 用途（”p“: 上传图片、"a": 上传头像） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |
> | path     | string   | 图片路径 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41007: 缺少图片
> - 41008: 缺少用途
> - 42010: 图片用途错误
> - 42011: 图片大小不得超过 5MB
> - 42012: 图片仅支持 jpg, png 格式
>

**示例**

```json
// request MultiForm
{
  "img": "file",
  "usage": "p"
}

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "path": "/picture/20201127172407i3xm4nhx23.png"
    }
}
```

---

### 识别鸟类

**接口功能**

> 根据图片识别鸟的种类，返回识别结果报告

**URL**

> /api/prediction/predict

**访问频率限制**

> 严格：2 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数 | 必选 | 类型   | 说明                                       |
> | :--- | :--- | :----- | ------------------------------------------ |
> | path | true | string | 图片文件在服务器的路径（上传图片时获取的） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明                                                         |
> | :------- | :------- | :----------------------------------------------------------- |
> | code     | int      | 状态码                                                       |
> | msg      | string   | 状态信息                                                     |
> | sequence | int      | 预测报告序列号                                               |
> | path     | string   | 图片路径                                                     |
> | result   | array    | 识别结果列表（具体内容类型参考示例，[鸟类名称、鸟类编号、置信度、信息]） |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41009: 缺少图片路径
> - 43006: 图片路径不存在
> - 45009: 图片路径类型错误

**示例**

```json
// request JSON
{
    "path": "/picture/20201130211502ltsuo9hsrq.jpg"
}

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "sequence": 1,
        "path": "/picture/20201130211502ltsuo9hsrq.jpg",
        "result": [
            {
                "label": "麻雀",
                "id": 118,
                "probability": 0.5456504225730896,
                "info": "麻雀是文鸟科麻雀属27种小型鸟类的统称。..."
            },
            {
                "label": "树雀",
                "id": 130,
                "probability": 0.3957599103450775,
                "info": "树麻雀也通称麻雀，是雀科、麻雀属的小型鸟类，..."
            },
            {
                "label": "崖燕",
                "id": 137,
                "probability": 0.02549450285732746,
                "info": "美洲燕，又称美洲香醇，原产于南美洲，学名香鹁，..."
            },
            {
                "label": "褐斑翅雀鹀",
                "id": 116,
                "probability": 0.020729750394821167,
                "info": "褐斑翅雀鹀是新大陆麻雀的一种，是Passerellidae家族的一种雀形目鸟类。...。 "
            },
            {
                "label": "黑喉麻雀",
                "id": 114,
                "probability": 0.0020368301775306463,
                "info": "黑喉漠鹀属小型鸣禽。一般主食植物种子。喙为圆锥形，...。"
            }
        ]
    }
}
```

---

### 获取识别报告

**接口功能**

> 根据识别报告序列号获取识别报告

**URL**

> /api/prediction/get_report

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> Query

**请求字段**

> | 参数     | 必选 | 类型 | 说明                               |
> | :------- | :--- | :--- | ---------------------------------- |
> | sequence | true | int  | 识别报告序列号（识别鸟类时获取的） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明                                                         |
> | :------- | :------- | :----------------------------------------------------------- |
> | code     | int      | 状态码                                                       |
> | msg      | string   | 状态信息                                                     |
> | sequence | int      | 预测报告序列号                                               |
> | path     | string   | 图片路径                                                     |
> | result   | array    | 识别结果列表（具体内容类型参考示例，[鸟类名称、鸟类编号、置信度、信息、示例图片]） |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41010: 缺少序列号
> - 43007: 报告不存在
> - 45010: 序列号类型错误

**示例**

```json
// request Query
// GET /api/prediction/get_report?sequence=1

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "sequence": 1,
        "path": "/picture/20201130211502ltsuo9hsrq.jpg",
        "result": [
            {
                "label": "麻雀",
                "id": 118,
                "probability": 0.5456504225730896,
                "info": "麻雀是文鸟科麻雀属27种小型鸟类的统称。...",
                "img": "/birds/118.jpg"
            },
            {
                "label": "树雀",
                "id": 130,
                "probability": 0.3957599103450775,
                "info": "树麻雀也通称麻雀，是雀科、麻雀属的小型鸟类，...",
                "img": "/birds/130.jpg"
            },
            {
                "label": "崖燕",
                "id": 137,
                "probability": 0.02549450285732746,
                "info": "美洲燕，又称美洲香醇，原产于南美洲，学名香鹁，...",
                "img": "/birds/137.jpg"
            },
            {
                "label": "褐斑翅雀鹀",
                "id": 116,
                "probability": 0.020729750394821167,
                "info": "褐斑翅雀鹀是新大陆麻雀的一种，是Passerellidae家族的一种雀形目鸟类。...。 ",
                "img": "/birds/116.jpg"
            },
            {
                "label": "黑喉麻雀",
                "id": 114,
                "probability": 0.0020368301775306463,
                "info": "黑喉漠鹀属小型鸣禽。一般主食植物种子。喙为圆锥形，...。",
                "img": "/birds/114.jpg"
            }
        ]
    }
}
```

---

### 获取鸟类信息

**接口功能**

> 根据鸟类 id，获取鸟类信息

**URL**

> /api/prediction/get_bird_info

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> Query

**请求字段**

> | 参数    | 必选 | 类型 | 说明                |
> | :------ | :--- | :--- | ------------------- |
> | bird_id | true | int  | 鸟类编号 （1～200） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明               |
> | :------- | :------- | :----------------- |
> | code     | int      | 状态码             |
> | msg      | string   | 状态信息           |
> | id       | int      | 鸟类编号（1～200） |
> | name     | string   | 鸟名               |
> | name_EN  | string   | 鸟英文名           |
> | info     | string   | 鸟类介绍           |
> | img      | string   | 鸟类示例图片       |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 41011: 缺少鸟类编号
> - 43008: 鸟类编号不存在
> - 45011: 鸟类编号类型错误

**示例**

```json
// request Query
// GET /api/prediction/get_bird_info?bird_id=1

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "bird_id": 1,
        "name": "黑脚信天翁",
        "name_EN": "Black footed Albatross",
        "info": "黑脚信天翁为鹱形目信天翁科下的一种大型海鸟，...。",
        "img": "/birds/1.jpg"
    }
}
```

---

### 注册

**接口功能**

> 用户注册

**URL**

> /api/account/register

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数     | 必选 | 类型   | 说明                                         |
> | :------- | :--- | :----- | -------------------------------------------- |
> | username | true | string | 用户名（长度 4～30，仅含字母、数字、下划线） |
> | password | true | string | 密码（长度6～20，要求含有数字和字母）        |
> | email    | true | string | 邮箱                                         |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41002: 缺少用户名
> - 41003: 缺少密码
> - 41004: 缺少邮箱
> - 42001: 用户名应不少于 4 位
> - 42002: 用户名应不多于 20 位
> - 42003: 用户名仅能含有字母、数字和下划线
> - 42004: 密码应不少于 6 位
> - 42005: 密码应不多于 20 位
> - 42006: 密码应仅包含合法字符
> - 42007: 密码必须包含数字
> - 42008: 密码必须包含字母
> - 42009: 邮箱格式错误
> - 43001: 用户名已存在
> - 43002: 邮箱已存在
> - 45002: 用户名类型错误
> - 45003: 密码类型错误
> - 45004: 邮箱类型错误

**示例**

```json
// request JSON
{
    "username": "leo123",
    "password": "leo123",
    "email": "leo123@qq.com"
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 登录

**接口功能**

> 用户登录

**URL**

> /api/account/login

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数     | 必选 | 类型   | 说明         |
> | :------- | :--- | :----- | ------------ |
> | username | true | string | 用户名或邮箱 |
> | password | true | string | 密码         |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41002: 缺少用户名
> - 41003: 缺少密码
> - 43003: 用户名不存在
> - 43004: 密码错误
> - 45002: 用户名类型错误
> - 45003: 密码类型错误

**示例**

```json
// request JSON
{
    "username": "leo123",
    "password": "leo123"
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 注销

**接口功能**

> 用户注销

**URL**

> /api/account/logout

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> 无格式

**请求字段**

> 无参数

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 44001: 未登陆

**示例**

```json
// request
// POST /api/account/logout

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 获取当前状态

**接口功能**

> 获取当前状态

**URL**

> /api/account/status

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> 无格式

**请求字段**

> 无参数

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明                   |
> | :------- | :------- | :--------------------- |
> | code     | int      | 状态码                 |
> | msg      | string   | 状态信息               |
> | login    | bool     | 是否登陆               |
> | username | string   | 如果登陆，返回用户名   |
> | nickname | string   | 如果登陆，返回昵称     |
> | avatar   | string   | 如果登陆，返回头像路径 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误

**示例**

```json
// request
// GET /api/account/status

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "login": false
    }
}

{
    "code": 20000,
    "msg": "成功",
    "data": {
        "login": true,
        "username": "leo123",
        "nickname": "leo",
        "avatar": "/avatar/20201210204936k7la40e45p.png"
    }
}
```

---

### 修改昵称

**接口功能**

> 修改昵称（要求登录）

**URL**

> /api/account/change_nickname

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数     | 必选 | 类型   | 说明              |
> | :------- | :--- | :----- | ----------------- |
> | nickname | true | string | 昵称（50 字以内） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41017: 缺少昵称
> - 42015: 昵称过长
> - 45017: 昵称类型错误

**示例**

```json
// request JSON
{
    "nickname": "leo"
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 修改头像

**接口功能**

> 修改头像（要求登录）

**URL**

> /api/account/change_avatar

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数   | 必选 | 类型   | 说明                                       |
> | :----- | :--- | :----- | ------------------------------------------ |
> | avatar | true | string | 图片文件在服务器的路径（上传图片时获取的） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41018: 缺少头像
> - 43006: 图片路径不存在
> - 45018: 头像类型错误

**示例**

```json
// request JSON
{
    "avatar": "/avatar/20201210204936k7la40e45p.png"
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 图片保存相册

**接口功能**

> 图片保存至相册（要求登录）

**URL**

> /api/gallery/save_in_gallery

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数      | 必选 | 类型   | 说明                                       |
> | :-------- | :--- | :----- | ------------------------------------------ |
> | path      | true | string | 图片文件在服务器的路径（上传图片时获取的） |
> | longitude | true | float  | 经度（无位置信息，则填 0.0）               |
> | latitude  | true | float  | 纬度（无位置信息，则填 0.0）               |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41009: 缺少图片路径
> - 41012: 缺少经度
> - 41013: 缺少纬度
> - 43006: 图片路径不存在
> - 43009: 图片已存在
> - 44001: 未登陆
> - 45009: 图片路径类型错误
> - 45012: 经度类型错误
> - 45013: 纬度类型错误

**示例**

```json
// request JSON
{
    "path": "/picture/20201130211502ltsuo9hsrq.jpg",
    "longitude": 0.0,
    "latitude": 0.0
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 获取我的相册

**接口功能**

> 获取我的相册（分页）（要求登录）

**URL**

> /api/gallery/get_my_gallery

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> Query

**请求字段**

> | 参数 | 必选  | 类型 | 说明                     |
> | :--- | :---- | :--- | ------------------------ |
> | num  | false | int  | 页号（不填，则默认为 1） |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明             |
> | :------- | :------- | :--------------- |
> | code     | int      | 状态码           |
> | msg      | string   | 状态信息         |
> | photo    | list     | 图片路径列表     |
> | count    | int      | 当前页的图片数量 |
> | num      | int      | 当前页号         |
> | has_next | bool     | 是否有下一页     |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 41014: 缺少页号
> - 42013: 页号超出范围
> - 44001: 未登陆
> - 45014: 页号类型错误

**示例**

```json
// request Query
// GET /api/gallery/get_my_gallery?num=1

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "photo": [
            "/picture/20201130223022vfz3sp9cvl.jpg",
            "/picture/20201130211502ltsuo9hsrq.jpg"
        ],
        "count": 2,
        "num": 1,
        "has_next": false
    }
}
```

---

### 发布动态

**接口功能**

> 发布动态（要求登录）

**URL**

> /api/post/give_post

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数    | 必选 | 类型   | 说明                                                     |
> | :------ | :--- | :----- | -------------------------------------------------------- |
> | path    | true | string | 图片文件在服务器的路径（上传图片时获取的，并已在相册的） |
> | content | true | string | 动态内容（400 字以内）                                   |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41009: 缺少图片路径
> - 41015: 缺少内容
> - 42014: 内容过长
> - 43006: 图片路径不存在
> - 44001: 未登陆
> - 45009: 图片路径类型错误
> - 45015: 内容类型错误

**示例**

```json
// request JSON
{
    "path": "/picture/20201130223022vfz3sp9cvl.jpg",
    "content": "Hello World"
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 获取全部动态

**接口功能**

> 获取全部动态（分页，每页最多 4 个动态）

**URL**

> /api/post/get_all_post

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> Query

**请求字段**

> | 参数 | 必选  | 类型 | 说明                     |
> | :--- | :---- | :--- | ------------------------ |
> | num  | false | int  | 页号（不填，则默认为 1） |

**响应格式**

> JSON

**响应字段**

> | 返回字段         | 字段类型 | 说明                                       |
> | :--------------- | :------- | :----------------------------------------- |
> | code             | int      | 状态码                                     |
> | msg              | string   | 状态信息                                   |
> | count            | int      | 当前返回动态的个数                         |
> | num              | int      | 当前页号                                   |
> | has_next         | bool     | 是否有下一页                               |
> | post             | list     | 动态列表                                   |
> | post:username    | string   | 当前动态的发布人用户名（有昵称，则为昵称） |
> | post:avatar      | string   | 当前动态的发布人头像                       |
> | post:content     | string   | 当前动态内容                               |
> | post:path     | string   | 当前动态的图片                               |
> | post:create_time | string   | 当前动态的发布时间                         |
> | post:address     | string   | 位置地点（无，则为空）                     |
> | post:like        | int      | 点赞数                                     |
> | post:is_liked    | bool     | 是否点过赞（**未登录用户无此字段**）       |
> | post:post_id     | int      | 当前动态 ID                                |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 41014: 缺少页号
> - 42013: 页号超出范围
> - 45014: 页号类型错误

**示例**

```json
// request Query
// GET /api/post/get_all_post?num=1

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "post": [
            {
                "username": "leo123",
                "avatar": "avatar/default.jpg",
                "content": "Hello World",
                "path": "/picture/20201130223022vfz3sp9cvl.jpg",
                "create_time": "2020-12-10 11:49:01",
                "address": "",
                "like": 0,
                "post_id": 1,
                "is_liked": false
            }
        ],
        "count": 1,
        "num": 1,
        "has_next": false
    }
}
```

---

### 获取热门动态

**接口功能**

> 获取热门动态（点赞数最多的 10 个动态）

**URL**

> /api/post/get_hot_post

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> 无格式

**请求字段**

> 无参数

**响应格式**

> JSON

**响应字段**

> | 返回字段         | 字段类型 | 说明                                       |
> | :--------------- | :------- | :----------------------------------------- |
> | code             | int      | 状态码                                     |
> | msg              | string   | 状态信息                                   |
> | count            | int      | 当前返回动态的个数                         |
> | post             | list     | 动态列表                                   |
> | post:username    | string   | 当前动态的发布人用户名（有昵称，则为昵称） |
> | post:avatar      | string   | 当前动态的发布人头像                       |
> | post:content     | string   | 当前动态内容                               |
> | post:path     | string   | 当前动态的图片                               |
> | post:create_time | string   | 当前动态的发布时间                         |
> | post:address     | string   | 位置地点（无，则为空）                     |
> | post:like        | int      | 点赞数                                     |
> | post:is_liked    | bool     | 是否点过赞（**未登录用户无此字段**）       |
> | post:post_id     | int      | 当前动态 ID                                |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误

**示例**

```json
// request Query
// GET /api/post/get_hot_post

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "post": [
            {
                "username": "leo123",
                "avatar": "avatar/default.jpg",
                "content": "Hello World",
                "path": "/picture/20201130223022vfz3sp9cvl.jpg",
                "create_time": "2020-12-10 11:49:01",
                "address": "",
                "like": 0,
                "post_id": 1,
                "is_liked": false
            }
        ],
        "count": 1
    }
}
```

---

### 获取我的动态

**接口功能**

> 获取我的动态（分页，每页最多 4 个动态）（要求登录）

**URL**

> /api/post/get_my_post

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> Query

**请求字段**

> | 参数 | 必选  | 类型 | 说明                     |
> | :--- | :---- | :--- | ------------------------ |
> | num  | false | int  | 页号（不填，则默认为 1） |

**响应格式**

> JSON

**响应字段**

> | 返回字段         | 字段类型 | 说明                                       |
> | :--------------- | :------- | :----------------------------------------- |
> | code             | int      | 状态码                                     |
> | msg              | string   | 状态信息                                   |
> | count            | int      | 当前返回动态的个数                         |
> | num              | int      | 当前页号                                   |
> | has_next         | bool     | 是否有下一页                               |
> | post             | list     | 动态列表                                   |
> | post:username    | string   | 当前动态的发布人用户名（有昵称，则为昵称） |
> | post:avatar      | string   | 当前动态的发布人头像                       |
> | post:content     | string   | 当前动态内容                               |
> | post:path     | string   | 当前动态的图片                               |
> | post:create_time | string   | 当前动态的发布时间                         |
> | post:address     | string   | 位置地点（无，则为空）                     |
> | post:like        | int      | 点赞数                                     |
> | post:is_liked    | bool     | 是否点过赞（**未登录用户无此字段**）       |
> | post:post_id     | int      | 当前动态 ID                                |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 41014: 缺少页号
> - 42013: 页号超出范围
> - 44001: 未登陆
> - 45014: 页号类型错误

**示例**

```json
// request Query
// GET /api/post/get_my_post?num=1

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "post": [
            {
                "username": "leo123",
                "avatar": "avatar/default.jpg",
                "content": "Hello World",
                "path": "/picture/20201130223022vfz3sp9cvl.jpg",
                "create_time": "2020-12-10 11:49:01",
                "address": "",
                "like": 0,
                "post_id": 1,
                "is_liked": false
            }
        ],
        "count": 1,
        "num": 1,
        "has_next": false
    }
}
```

---

### 点赞

**接口功能**

> 点赞（要求登录）

**URL**

> /api/post/like_post

**访问频率限制**

> 中等：1 秒 2 次

**请求方式**

> POST

**请求格式**

> JSON

**请求字段**

> | 参数    | 必选 | 类型 | 说明    |
> | :------ | :--- | :--- | ------- |
> | post_id | true | int  | 动态 ID |

**响应格式**

> JSON

**响应字段**

> | 返回字段 | 字段类型 | 说明     |
> | :------- | :------- | :------- |
> | code     | int      | 状态码   |
> | msg      | string   | 状态信息 |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误
> - 40001: JSON 解析错误
> - 41016: 缺少动态 ID
> - 43010: 动态不存在
> - 43011: 已点赞
> - 45016: 动态 ID 类型错误

**示例**

```json
// request JSON
{
    "post_id": 1
}

// response JSON
{
    "code": 20000,
    "msg": "成功"
}
```

---

### 获取地点

**接口功能**

> 获取地点（有位置信息的动态）

**URL**

> /api/post/get_points

**访问频率限制**

> 宽松：1 秒 5 次

**请求方式**

> GET

**请求格式**

> 无格式

**请求字段**

> 无参数

**响应格式**

> JSON

**响应字段**

> | 返回字段        | 字段类型 | 说明             |
> | :-------------- | :------- | :--------------- |
> | code            | int      | 状态码           |
> | msg             | string   | 状态信息         |
> | count           | int      | 当前返回点的个数 |
> | point           | list     | 点的列表         |
> | point:content   | string   | 点对应动态的内容 |
> | point:path | string   | 点对应动态的图片 |
> | point:address   | string   | 点对应的地址     |
> | point:longitude | float    | 点对应的经度     |
> | point:latitude  | float    | 点对应的纬度     |

**响应状态码与状态信息**

> - 20000: 成功
> - 50000: 意外错误
> - 50403: Forbidden
> - 40000: 请求方法错误

**示例**

```json
// request JSON
// GET /api/post/get_points

// response JSON
{
    "code": 20000,
    "msg": "成功",
    "data": {
        "point": [
            {
                "content": "Hello World",
                "path": "/picture/20201130223022vfz3sp9cvl.jpg",
                "address": "某个城市",
                "longitude": 0,
                "latitude": 0
            }
        ],
        "count": 1
    }
}
```

---

