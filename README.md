# 金蝶KIS专业版财务凭证同步WebAPI接口

------

- [接口简介](#接口简介)
- [接口说明](#接口说明)
- [登录](#登录)
- [退出登录](#退出登录)
- [会计科目](#会计科目)
- [凭证字](#凭证字)
- [同步凭证](#同步凭证)
- [查看核算项目类型](#查看核算项目类型)
- [查看核算项目](#查看核算项目)
- [获取凭证JSON模板](#获取凭证JSON模板)
- [C#调用示例](#C#调用示例)
- [VB.net调用示例](#VB.net调用示例)

## 接口简介

本文档为金蝶KIS专业版财务凭证同步WebAPI接口技术文档，本接口可以供任何第三方业务系统调用生成金蝶KIS专业版财务记账凭证。
1. 调用登录接口，通过AcctKey区分不同帐套；
2. 获得用户token（每次登录会**重新生成**，原token失效）；
3. 调用凭证同步接口（按格式构造参数）；
4. 同步完毕后，调用退出接口以使token失效<br/>

- **AcctKey**：金蝶KIS专业版帐套ID，用此ID来连接不同的帐套。
- **Token**:登录成功后返回给调用端的用户身份认证字符串，访问其他所有接口均需提供。

- **接口地址**：http://服务器IP:端口号/uxi/api，实际调用时请将服务器IP替换为实际IP或域名，端口号替换为实际端口号。例如http://192.168.1.106:90/uxi/api。

## 接口说明

所有接口以RESTful的方式访问，参数及返回值均为采用UTF-8编码的标准JSON格式字符串。

## 登录

**简要描述：** 登录到金蝶KIS专业版

**请求 URL：** `/login`

**请求方式：** POST

**请求参数：**

```
{
    "user":                  //用户登录信息段
    {
        "ID":0,              //用户内码，固定为0
        "Name":"Manager",    //用户名称，与专业版登录用户名一致
        "Password":""        //用户登录密码，数据来自select FSID from t_user where FUserID=用户内码，参数必须为FSID字段的值，不能为密码明文
    },
    "AcctKey":24             //帐套ID，数据来自select FAcctID from acctctl..t_ad_kdAccount_gl
}
```

**返回串及字段说明**

```
{
    "Login":                                             //登录结果信息段
    {
        "Result": 1,                                     //登录结果：1成功，其他值失败
        "Message": "登录验证成功"                         //登录结果的具体描述
    },
    "User":                                              //当前登录用户信息段
    {
        "ID": 16394,                                     //用户内码
        "Name": "Manager",                               //用户名
        "Password": ""                                   //登录密码，固定为空白
    },
    "Secure":                                            //安全认证信息段
    {
        "AcctKey": 24,                                   //帐套ID，此ID区分不同帐套
        "Token": "09400343c35d3b39d8970831e3b1f502"      //用户Token，访问其他接口时用来验证用户登录身份
    }
}

```


## 退出登录

**简要描述：** 功能接口调用完毕后，请调用此接口退出，以使用户Token失效。

**请求 URL：** `/logout`

**请求方式：** POST

**请求参数：**

```
{
    "AcctKey": 24,                                //帐套ID
    "Token": "09400343c35d3b39d8970831e3b1f502"   //用户Token
}
```

**返回串及字段说明**

```
{
    "Result": 1,                                  //接口执行结果：1成功，其他值失败
    "Message": "当前用户token清除成功"             //执行结果的具体文字描述
}

```

## 会计科目

**简要描述：** 获取金蝶KIS专业版指定帐套的会计科目列表

**请求 URL：** `/accounts`

**请求方式：** POST

**请求参数：**

```
{
    "AcctKey": 24,                                //帐套ID
    "Token": "09400343c35d3b39d8970831e3b1f502"   //用户Token
}
```

**返回串及字段说明**

```
{
    "Result": 1,                                  //接口执行结果：1成功，其他值失败
    "Count": 175,                                 //总科目数
    "Account": [                                  //科目数据，此数据为列表Array形式
        {
            "Name": "库存现金",                    //科目名称
            "FullName": "库存现金",                //科目完整名称，如：银行存款-基本户
            "Group": {                            //科目类别，如流动资产、长期资产、流动负债等
                "ID": 1,                          //科目类别内码
                "Number": "101",                  //科目类别编码
                "Name": "流动资产"                 //科目类别名称
            },
            "Level": 1,                           //科目级别，即第几级科目
            "Detail": true,                       //是否明细科目，True为是，False为否
            "ParentID": 0,                        //上级科目内码，若自身为明细科目，则该值为0
            "DC": 1,                              //余额借贷方向，1为借方，0为贷方
            "CurrencyID": 0,                      //币别内码
            "Quantity": false,                    //科目是否进行数量金额核算 
            "Unit": {                             //若进行数量金额核算，数量的计量单位信息
                "ID": 0,                          //计量单位内码，未数量金额核算的科目 值为0
                "Number": "*",                    //计量单位编码，未数量金额核算的科目 值为*
                "Name": "*"                       //计量单位名称，未数量金额核算的科目 值为*
            },
            "IsCash": true,                       //是否现金科目
            "IsBank": false,                      //是否银行科目
            "ItemsID": 0,                         //科目下挂核算项目情况，0代表无核算项目
            "ItemsCount": 0,                      //科目下挂几个核算项目
            "Items":[                             //科目下挂核算项目详情，此数据为列表Array形式。如无核算项目，则此处为空值
                {
                    "ID":0,                       //核算项目类别内码
                    "Number":"*",                 //核算项目类别编码
                    "Name":"*"                    //核算项目类别名称
                }
            ],                          
            "Forbidden": 0,                       //科目是否被禁用，0：正常使用；1：禁用
            "Message": "",                        //附加信息
            "ID": 1000,                           //科目内码
            "Number": "1001",                     //科目编码 
            "UserID": 16394,                      //访问此接口的用户内码
            "UserName": "Manager"                 //访问此接口的用户名
        }, {
            "Name": "被套期项目",
            "FullName": "被套期项目",
            "Group": {
                "ID": 6,
                "Number": "601",
                "Name": "共同类"
            },
            "Level": 1,
            "Detail": true,
            "ParentID": 0,
            "DC": 1,
            "CurrencyID": 1,
            "Quantity": false,
            "Unit": {
                "ID": 0,
                "Number": "*",
                "Name": "*"
            },
            "IsCash": false,
            "IsBank": false,
            "ItemsID": 0,
            "ItemsCount": 0,
            "Items": [],
            "Forbidden": 0,
            "Message": "",
            "ID": 1064,
            "Number": "3202",
            "UserID": 16394,
            "UserName": "Manager"
        }
    ],
    "Message": "获取会计科目列表成功",                 //执行结果的具体文字描述
    "UserID": 16394,
    "UserName": "Manager"
}

```

## 凭证字

**简要描述：** 返回指定帐套的所有凭证字

**请求 URL：** `/words`

**请求方式：** POST

**请求参数：**

```
{
    "AcctKey": 24,                                //帐套ID
    "Token": "09400343c35d3b39d8970831e3b1f502"   //用户Token
}
```

**返回串及字段说明**

```
{
    "Result": 1,                            //接口执行结果：1成功，其他值失败
    "Word": [                               //凭证字信息，此数据为列表Array形式
        {
            "ID": 2,                        //凭证字内码 
            "Name": "记"                    //凭证字
        },
        {
            "ID": 3,
            "Name": "转"
        },
        {
            "ID": 4,
            "Name": "现收"
        },
        {
            "ID": 5,
            "Name": "现付"
        },
        {
            "ID": 6,
            "Name": "银收"
        },
        {
            "ID": 7,
            "Name": "银付"
        }
    ],
    "Message": "成功读取所有凭证字",         //执行结果的具体文字描述
    "UserID": 16394,                       //访问此接口的用户内码
    "UserName": "Manager"                  //访问此接口的用户名
}

```

## 同步凭证

**简要描述：** 在金蝶KIS专业版指定帐套里创建一张记账凭证

**请求 URL：** `/add`

**请求方式：** POST

**请求参数：**

```
{
  "Head":{                                 //凭证头信息
    "Word":"记",                           //凭证字，注意此处为汉字，而不是内码
    "Number":0,                            //凭证号，如果为0则按金蝶KIS专业版已有凭证号顺序递增
    "Attachment":2,                        //附件数
    "Date":"2017-06-30",                   //凭证日期
    "TranDate":"2017/06/30",               //业务日期
    "Reference":"凭证接口提供数据"           //参考信息 
  },
  "Entries":[                              //凭证分录，至少要有两条且借贷平衡
      {
        "Explanation":"购买音箱一台",       //摘要
        "AcctNumber":"1411.02",            //科目编码
        "TransNumber":"",                  //往来业务编码，可以为空 
        "Debit":198.0,                     //借方金额
        "Credit":0.0,                      //贷方金额
        "Qty":1.0,                         //采用数量金额核算的科目的分录数量，未数量金额核算此处为0  
        "Price":198.0,                     //采用数量金额核算的科目的分录单价，未数量金额核算此处为0
        "Currency":"RMB",                  //币别编码
        "Items":"269,201,274"              //核算项目内码，科目下挂多个核算项目，内码之间以,（英文逗号）分隔 
        },
      {
        "Explanation":"现金支付",
        "AcctNumber":"1001",
        "TransNumber":"",
        "Debit":0.0,
        "Credit":198.0,
        "Qty":0.0,
        "Price":0.0,
        "Currency":"RMB",
        "Items":""
      }
  ],
  "Secure":{                                      //认证信息
    "AcctKey":24,                                 //帐套ID
    "Token":"09400343c35d3b39d8970831e3b1f502"    //用户Token
  }
}

```

**返回串及字段说明**

```
{
    "Result": 1,                     //接口执行结果：1成功，其他值失败
    "Message": "保存凭证成功"         //执行结果的具体文字描述
}
```

## 查看核算项目类型

**简要描述：** 有些会计科目下挂一个或多个核算项目，凭证接口在提交数据时，需要提供核算项目的内码，本接口是查看核算项目内码接口的前置接口。需先通过本接口获取核算项目类型的内码，然后提交核算项目类型内码至查看核算项目接口。

**请求 URL：** `/itemtype`

**请求方式：** POST

**请求参数：**

```
{
    "AcctKey": 24,                                //帐套ID
    "Token": "09400343c35d3b39d8970831e3b1f502"   //用户Token
}
```

**返回串及字段说明**

```
{
    "Result": 1,                           //接口执行结果：1成功，其他值失败
    "Message": "成功获取所有核算项目类别",   //执行结果的具体文字描述
    "Type": [                              //核算项目类型信息，金蝶里可以自定义核算项目，因此每个帐套返回值可能都不一样
        {
            "ID": 1,                       //核算项目类型内码，如1为客户，2为部门等
            "Name": "客户"                 //核算项目类别名称，如客户、部门等 
        },
        {
            "ID": 2,
            "Name": "部门"
        },
        {
            "ID": 3,
            "Name": "职员"
        },
        {
            "ID": 4,
            "Name": "物料"
        },
        {
            "ID": 5,
            "Name": "仓库"
        },
        {
            "ID": 6,
            "Name": "备注"
        },
        {
            "ID": 7,
            "Name": "计量单位"
        },
        {
            "ID": 8,
            "Name": "供应商"
        },
        {
            "ID": 9,
            "Name": "现金流量项目"
        }
    ]
}

```

## 查看核算项目

**简要描述：** 根据itemtype接口获得核算项目类别内码后，提交具体内码可以查看该类别的核算项目。例如，要查看客户信息，就在参数里提交1。依次类推。

**请求 URL：** `/item`

**请求方式：** POST

**请求参数：**

```
{
    "TypeID":1,                                        //核算项目类别内码，根据itemtype接口获得。1 客户；2 部门；3 职员；4 物料；5 仓库；7 计量单位；8 供应商
    "Secure":{                                         //认证信息
        "AcctKey": 24,                                 //帐套ID
        "Token": "dd98bb330b927b6a881efbdaef6035df"    //用户Token
    }
}
```

**返回串及字段说明**

```
{
    "Result": 1,                                  //接口执行结果：1成功，其他值失败
    "Message": "成功获取指定类别的核算项目",        //执行结果的具体文字描述
    "Data": [                                     //核算项目具体内容，列表
        {
            "ID": 227,                            //核算项目内码
            "Number": "007",                      //核算项目编码
            "Name": "爱华电脑公司"                 //核算项目名称
        },
        {
            "ID": 229,
            "Number": "009",
            "Name": "爱特儿信息有限公司"
        },
        {
            "ID": 226,
            "Number": "006",
            "Name": "奥德赛电子有限公司"
        }

    ]
}

```

## 获取凭证JSON模板

**简要描述：** 获取凭证模板

**请求 URL：** `/getvouchertemplate`

**请求方式：** GET

**请求参数：**

| 参数名 | 必选 | 类型 | 说明 |
|:----|:---:|:-----:|:-----|
| `i` | 是 | `int` | 1，2，3，分别显示不同的模板数据 |

**返回串及字段说明**

- 与add接口提交参数一致

## C#调用示例

```
using System;
using System.Net;

namespace VisitWebAPI_C
{
    class Program
    {
        static void Main(string[] args)
        {
            //以访问登录接口为例
            string result_data;
            string url = "http://192.168.1.102/UXI/api/login";
            //含义：用户Manager登录，该用户密码为空，要登录的帐套内码为24
            //具体提交参数格式请阅读接口技术手册
            string param = "{\"user\":{\"ID\":0,\"Name\":\"Manager\",\"Password\":\"\"},\"AcctKey\":24}";
            WebClient client = new WebClient();
            client.Headers.Add("Content-Type", "application/json;charset=utf-8");
            //给访问webapi的客户端一个标识
            //从而方便从日志中甄别出非法访问客户端
            //也可以不要此句
            client.Headers.Add("User-Agent", "QenghooRuntime/1.0");
            result_data = client.UploadString(url, "POST", param);
            Console.WriteLine(result_data);
            Console.ReadLine();
        }
    }
}

```

## VB.net调用示例
```
Imports System.Net
Module Module1

    Sub Main()
        '以访问登录接口为例
        Dim result_data As String
        '此处将192.168.1.102替换为服务器实际地址
        Dim url As String = "http://192.168.1.102/UXI/api/login"
        '含义：用户Manager登录，该用户密码为空，要登录的帐套内码为24
        '具体提交参数格式请阅读本技术手册
        Dim param As String = "{""user"":{""ID"":0,""Name"":""Manager"",""Password"":""""},""AcctKey"":24}"
        Dim client As New WebClient
        client.Headers.Add("Content-Type", "application/json;charset=utf-8")
        '给访问webapi的客户端一个标识
        '从而方便从日志中甄别出非法访问客户端
        '也可以不要此句
        client.Headers.Add("User-Agent", "QenghooRuntime/1.0")
        result_data = client.UploadString(url, "POST", param)
        Console.WriteLine(result_data)
        Console.ReadLine()
    End Sub

End Module
```
