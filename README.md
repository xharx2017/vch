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
<table>
   <tr>
      <td>AcctKey</td>
      <td>帐套ID，数据来自select FAcctID from acctctl..t_ad_kdAccount_gl</td>
   </tr>
   <tr>
      <td> </td>
   </tr>
   <tr>
      <td>user</td>
      <td>用户登录信息</td>
   </tr>
   <tr>
      <td>ID</td>
      <td>用户内码，固定为0</td>
   </tr>
   <tr>
      <td>Name</td>
      <td>用户名称，与专业版登录用户名一致</td>
   </tr>
   <tr>
      <td>Password</td>
      <td>用户登录密码，数据来自select FSID from t_user where FUserID=用户内码，参数必须为FSID字段的值，不能为密码明文。例如用户密码为123456，FSID值为)  F ", ,P T #8 *P!D &D 80!N &@ &#60;0 C '&#60; : !M &4 )0 Q #@ ,0 Q #( ,  P #$ ,@ P #$，提交参数必须为这段加密后的密文</td>
   </tr>
   <tr>
      <td></td>
   </tr>
   <tr>
      <td></td>
   </tr>
</table>
