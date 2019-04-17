# request和response

---

### request 类
>Request对象包括了如下属性和方法

method    表示请求中使用的HTTP方法的字符串。这保证是大写的  
path       表示所请求页面的完整路径的字符串，不包括方案或域。  
body    原始HTTP请求主体  
scheme    表示请求方案（http或https 通常）的字符串  
encoding    表示用于解码表单提交数据的当前编码的字符串  
GET      类似于字典的对象，包含所有给定的HTTP GET参数  
POST   类似字典的对象，包含所有给定的HTTP POST参数，前提是请求包含表单数据  
COOKIES    包含所有cookie的字典。键和值是字符串  

- **META       包含所有可用HTTP标头的字典。可用的头文件取决于客户端和服务器，示例：**  
> CONTENT_LENGTH - 请求正文的长度（作为字符串） 
> CONTENT_TYPE - 请求正文的MIME类型。  
> HTTP_ACCEPT - 响应的可接受内容类型。  
> HTTP_ACCEPT_ENCODING - 可接受的响应编码。  
> HTTP_ACCEPT_LANGUAGE - 响应的可接受语言。  
> HTTP_HOST - 客户端发送的HTTP主机头。  
> HTTP_REFERER - 引用页面，如果有的话。  
> HTTP_USER_AGENT - 客户端的用户代理字符串。  
> QUERY_STRING - 查询字符串，作为单个（未解析的）字符串。  
> REMOTE_ADDR - 客户端的IP地址。  
> REMOTE_HOST - 客户端的主机名。  
> REMOTE_USER - Web服务器验证的用户（如果有）。  
> REQUEST_METHOD- 一个字符串，如"GET"或"POST"。  
> SERVER_NAME - 服务器的主机名。  
> SERVER_PORT - 服务器的端口（作为字符串）。  
>     
    
### response 类
- **属性**

> HttpResponse.content     响应的内容。bytes类型。  
> HttpResponse.charset    编码的字符集。 如果没指定，将会从content_type中解析出来。  
> HttpResponse.status_code    响应的状态码，比如200。  
> HttpResponse.reason_phrase    响应的HTTP原因短语。 使用标准原因短语。  
> 除非明确设置，否则reason_phrase由status_code的值决定。  
> HttpResponse.streaming    这个属性的值总是False。由于这个属性的存在，使得中间件能够区别对待流式响应和常规响应。  
> HttpResponse.closed如果响应已关闭，那么这个属性的值为True。  
