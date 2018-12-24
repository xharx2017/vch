# 金蝶KIS专业版财务凭证同步WebAPI接口
金蝶KIS专业版财务凭证同步WebAPI接口调用描述
# 接口
## 1.1登录
**调用示例**  
http://服务器IP:端口号/uxi/api/login  
**接口类型**  
POST  
**参数格式**  
{"user":{"ID":0,"Name":"Manager","Password":""},"AcctKey":24}  
**参数说明**

|字段|描述|
| :- | :- |
|AcctKey|帐套ID，数据来自select FAcctID from acctctl..t_ad_kdAccount_gl|
|user|用户登录信息|
|　ID|用户内码，固定为0|
|　Name|用户名称，与专业版登录用户名一致|
|　Password|用户登录密码，数据来自select FSID from t_user where FUserID=用户内码，参数必须为FSID字段的值，不能为密码明文|
