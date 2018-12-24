# 金蝶KIS专业版财务凭证同步WebAPI接口
金蝶KIS专业版财务凭证同步WebAPI接口调用描述
# 接口
## 1.1登录
**调用示例**  
http://服务器IP:端口号/uxi/api/login  
**接口类型**  
POST  
**参数格式及说明**  
{"user":{"ID":0,"Name":"Manager","Password":""},"AcctKey":24} 

{"user":                     \\登录用户信息
    {
        "ID":0,              \\用户内码，登录时此值固定为0 
        "Name":"Manager",    \\用户名，与金蝶KIS专业版登录用户名一致
        "Password":""        \\登录密码，此处提交金蝶KIS专业版用户密码的密文，即select FSID from t_user
    },
 "AcctKey":24                \\帐套ID，select FAcctID from acctctl..t_ad_kdAccount_gl 
}  
