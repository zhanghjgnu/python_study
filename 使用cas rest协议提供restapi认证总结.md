# 使用cas rest协议提供restapi认证总结
参考文档https://apereo.github.io/cas/6.2.x/protocol/REST-Protocol.html
## 0.cas 认证原理
    从结构上看，CAS 包含两个部分： CAS Server 和 CAS Client，只要将开发单点登录的系统集成cas的客户端，然后部署好服务端，就可以实现多系统的单点登录。
    CAS Server 需要独立部署，主要负责对用户的认证工作；CAS Client 负责处理对客户端受保护资源的访问请求，需要登录时，重定向到 CAS Server。CAS Client 与受保护的客户端应用部署在一起，以 Filter 方式保护受保护的资源。对于访问受保护资源的每个 Web 请求，CAS Client 会分析该请求的 Http 请求中是否包含 Service Ticket，如果没有，则说明当前用户尚未登录，于是将请求重定向到指定好的 CAS Server 登录地址，并传递 Service （也就是要访问的目的资源地址），以便登录成功过后转回该地址
## 1 先在build.gradle中增加
```implementation "org.apereo.cas:cas-server-support-rest:${project.'cas.version'}"```
重新打包gradlew clean copyCasConfiguration build
把生成的war重新部署到tomcat9.0.37下

##2 获取TGT(Ticket Granting Ticket)
官方的说法是
```POST /cas/v1/tickets HTTP/1.0
'Content-type': 'Application/x-www-form-urlencoded'
username=battags&password=password&additionalParam1=paramvalue
```
我们采用两种办法，一种是linux客户端的curl
```curl -k -i -X POST -d "username=9999&password=123456&service=https://www.baidu.com" https://clover.app-hos.com:8443/cas/v1/tickets```

另一种办法是使用postman:
方法选择post，url写https://clover.app-hos.com:8443/cas/v1/tickets
header为空，body选择x-www-form-urlencoded,下面key和value填写以下三项
    username 9999
    password 123456
    service https://www.baidu.com
提交后返回如下
```
<!DOCTYPE HTML PUBLIC \"-//IETF//DTD HTML 2.0//EN\"><html><head><title>201 CREATED</title></head><body><h1>TGT Created</h1><form action="https://clover.app-hos.com:8443/cas/v1/tickets/TGT-3-9bj4SEbn3sQhEJ1i7ePsKfFgR8hsawRQ6mJ9DVzlZI-Xp6Jpl60BvanTZkPT9odOAws-hecs-x-xlarge-2-linux-20200618093719" method="POST">Service:<input type="text" name="service" value=""><br><input type="submit" value="Submit"></form></body></html>
```
可以看到返回了tickets=TGT-3-9bj4SEbn3sQhEJ1i7ePsKfFgR8hsawRQ6mJ9DVzlZI-Xp6Jpl60BvanTZkPT9odOAws-hecs-x-xlarge-2-linux-20200618093719

## 3 用TGT申请ST票据(Service Ticket)
官方给出的格式
```POST /cas/v1/tickets/{TGT id} HTTP/1.0
service={form encoded parameter for the service url}```
或者续订
```POST /cas/v1/tickets/{TGT id} HTTP/1.0
service={form encoded parameter for the service url}&renew=true&username=battags&password=password```

我们同样可以采用两种方法实现:
 方法1:
```curl -k -i -X POST -d "service=https://www.baidu.com" https://clover.app-hos.com:8443/cas/v1/tickets/TGT-3-9bj4SEbn3sQhEJ1i7ePsKfFgR8hsawRQ6mJ9DVzlZI-Xp6Jpl60BvanTZkPT9odOAws-hecs-x-xlarge-2-linux-20200618093719
```
  方法2:用postman实现
 提交方式选择post，url输入 https://clover.app-hos.com:8443/cas/v1/tickets/TGT-3-9bj4SEbn3sQhEJ1i7ePsKfFgR8hsawRQ6mJ9DVzlZI-Xp6Jpl60BvanTZkPT9odOAws-hecs-x-xlarge-2-linux-20200618093719,body同样选择x-www-form-urlencoded,里面的key和value如下
    service  https://www.baidu.com
 提交后返回ST-2-OWl8OqX91EdG6oKQaijfrI-A-SQ-hecs-x-xlarge-2-linux-20200618093719
 这样就成功的获得了ST

## 4. 用ST票据去访问服务
使用浏览器访问https://www.baidu.com/?ticket=ST-2-OWl8OqX91EdG6oKQaijfrI-A-SQ-hecs-x-xlarge-2-linux-20200618093719

## 5. restful接口验证ST票据
官方的提供:
GET /cas/p3/serviceValidate?service={service url}&ticket={service ticket}
因为票据是一次性使用的，所以需要再申请一个ticket，并进行验证。
使用postman
 提交方式选择get，url=https://clover.app-hos.com:8443/cas/p3/serviceValidate?service=https://www.baidu.com/&ticket=ST-3-f7Z-YexEp9UlH1mLnrqM5g4tGTM-hecs-x-xlarge-2-linux-20200618093719
  票据验证没能通过，应该是动作太慢超过10秒过期啦
返回
```<cas:serviceResponse xmlns:cas='http://www.yale.edu/tp/cas'>
    <cas:authenticationFailure code="INVALID_TICKET">Ticket &#39;ST-4-JjTEjon6JmaTYG6WCBG-WQIHSLg-hecs-x-xlarge-2-linux-20200618093719&#39; not recognized</cas:authenticationFailure>
</cas:serviceResponse>
```

## 6. 查询TGT状态
官方格式
   GET /cas/v1/tickets/TGT-fdsjfsdfjkalfewrihfdhfaie HTTP/1.0
postman用get通过

## 7. 销毁TGT
官方格式
DELETE /cas/v1/tickets/TGT-fdsjfsdfjkalfewrihfdhfaie HTTP/1.0

再次查询TGT状态会提示Ticket could not be found


## 8 关于过期策略
一旦TGT过期，所有的系统就都不能访问了，ST是针对每一个系统的。两者都是有默认的过期策略，都可以根据自己的需求设置自己的需要的过期策略。
```
   # Default Expiration Policy
   # tgt.maxTimeToLiveInSeconds=28800
   # tgt.timeToKillInSeconds=7200
```
    上面是cas.properties中的默认过期策略，tgt.maxTimeToLiveInSeconds指的是TGT的最大生存时间，28800秒，也就是八小时。tgt.timeToKillInSeconds是指在用户没有对系统进行任何操作的情况下，7200秒之后，也就是两个小时之后TGT会过期。过期之后需要重新登录操作。 
```
# Service Ticket Timeout 
#st.timeToKillInSeconds=10
# st.numberOfUses=1
```
    
上面是ST的默认过期策略，st.timeToKillInSeconds=10说明当你访问一个应用系统时，cas server签发了一张票据，你需要在十秒钟之内拿着这种ST去server进行校验，过了10秒钟就过期了，系统也就访问不了；st.numberOfUses=1指的是ST可以用几次才过期，默认是用过一次就过期。

参考
    https://blog.csdn.net/lan_13217/article/details/84858766
    https://www.pianshen.com/article/7560315322/
