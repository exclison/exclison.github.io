---
title: 关于浏览器的预检(OPTION)请求
date: 2022-11-16 16:53:50
tags: 概念
---
## 关于浏览器的预检(OPTION)请求

OPTION请求没有附带请求数据,响应体也为空



### 1. OPTION预检请求的作用

OPTION请求用于获取目的资源所支持的通信选项

* 检测服务器所支持的请求方法

* CORS中的预检请求

  CORS规范要求,对那些可能对服务器数据产生副作用的HTTP请求方法(特别是GET以外的HTTP请求,或者搭配某些MIME类型的POST请求),浏览器必须首先使用OPTIONS方法发起一个预检请求(preflight request),从而获知服务端是否允许该跨域请求.服务器确认允许之后,才发起实际的HTTP请求.在预检请求的返回中,服务端也可以通知客户端,是否需要携带身份凭证(包括Cookies和HTTP认证相关数据)

  

### 2. 什么场景会出发CORS的预检请求

**在CORS机制中,客户端(浏览器)将请求分为两种:**

1. 简单请求(需同时满足以下条件)

   * 请求方法是以下三种之一:GET,HEAD,POST
   * HTTP的头信息不超出以下几种字段:Accept,Accept-Language,Content-Language,Last-Event-ID,Content-Type
   * Content-Type的值仅限以下三种:text\plain,multipart/form-data,application/x-www-form-urlencoded

2. 非简单请求(但凡不同时满足上面两个条件,就属于非简单请求)

   会触发浏览器发生预检请求,这是浏览器行为.“预检请求“的使用,可以避免跨域请求对服务器的用户数据产生未预期到影响.

   ```javascript
   axios.get('/api/user',{headers:{'token':'thisisatoken'}})
   ```

   上述代码中添加了token 是一个复杂请求,会触发浏览器的预检请求,可以在服务端解决

   ```javascript
   res.writeHead(200,{
     "Access-Control-Allow-Origin":"http://localhost:3000",//解决跨域问题,放开请求的网站
     "Access-Control-Allow-Headers":"token,Content-Type",//解决预检请求问题
     "Access-Control-Allow-Headers":"PUT",//放开PUT请求
   })
   ```

   如果类似浏览器这种，包含 CORS 机制的客户端发送的请求，每次都要经过一个复杂逻辑才能知道自己是否跨域，服务器的压力和用户体验是不理想的，那么预检请求就孕育而生：发送实际请求前，先发送预检请求询问服务器是否允许跨域，不允许就不发送实际请求，服务器只需要对预检请求进行跨域处理。
    这样来看，在 CORS 机制中，发送预检请求是一种保护机制，保护资源不被未授权的请求修改。和授权服务很像，预检请求通过了，浏览器后续对同一服务的请求，不需要做跨域询问，服务端不想支持跨域访问，啥也不用做。

   

## 3. CORS

Cross-Origin Resource Sharing,跨域资源共享,是W3C推荐使用的一种跨域的访问验证机制,它由一系列传输的HTTP头组成,这些HTTP头决定浏览器是否阻止前端JavaScript代码获取跨域请求的响应.

这种机制让Web应用服务器能支持跨站访问控制,使跨站数据传输更加安全,减轻跨域HTTP请求的风险.CORS验证机制需要客户端和服务端协同处理.



## 4.同源策略

出于安全考虑(CSRF(Cross-site request forgery)跨站请求伪造),浏览器限制**从脚本中发起的跨域HTTP请求**.默认的安全限制为同源策略,即JavaScript或Cookie只能访问同域下的内容.例如:XMLHttpRequest和Fetch遵循同源策略.因此,使用XMLHttpRequest 和Fetch API的Web应用程序只能将HTTP请求发送到其自己的域.



## 5. 同源

如果两个URL的协议protocol、端口prot、域名host都相同的话，则这两个URL是同源的。
⚠️ Internet Explorer 的同源策略没有将端口号纳入到同源策略的检查中。


