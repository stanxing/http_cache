title: cdn
speaker: stanxing
url: https://github.com/ksky521/nodeppt
transition: cards
files: /js/demo.js,/css/demo.css

[slide]

# cdn
## 演讲者：stanxing

[slide]
# 缓存
## 缓存类型
- 私有缓存： 浏览器缓存，只能被一个用户使用
- 共享缓存： CDN，可以被多个用户使用
## 缓存控制类型
- 强制缓存
    - Cache-control http/1.1
    - Expires http/1.0
- 协商缓存
    - Last-Modified/If-Modified-Since
    - Etag/If-None-Match
[slide]
# 缓存
## 普遍的缓存操作的目标
- 一个检索请求的成功响应：一般只能存储对GET请求的响应，对于GET请求，响应状态码为200
- 不变的重定向： 301
- 错误响应： 404的一个页面
- 不完全的响应： 状态码206，只返回局部的信息
- 除了 GET 请求外，如果匹配到作为一个已被定义的cache键名的响应
[slide]
# 缓存控制
## 强制缓存
### Cache-control 头
- http/1.1定义的Cache-control头用来区分对缓存机制的支持情况，请求头和响应头都支持这个属性
- 客户端请求指令
    - Cache-Control: max-age=<seconds>
    - Cache-Control: max-stale[=<seconds>]
    - Cache-Control: min-fresh=<seconds>
    - Cache-control: no-cache 
    - Cache-control: no-store
    - Cache-control: no-transform
    - Cache-control: only-if-cached
- 服务端响应指令
    - Cache-control: must-revalidate
    - Cache-control: no-cache
    - Cache-control: no-store
    - Cache-control: no-transform
    - Cache-control: public
    - Cache-control: private
    - Cache-control: proxy-revalidate
    - Cache-Control: max-age=<seconds>
    - Cache-control: s-maxage=<seconds>
[slide]
# Cache-control 头
### 可缓存性
- public： 服务端指令，表明响应可以被任何对象缓存（包括发送请求的客户端，中间的代理服务器）
- private： 服务端指令，表明响应只能被单个用户缓存（即中间的代理服务器不能缓存它）
- no-cache： 强制所有缓存了该响应的缓存用户，在使用已存储的缓存数据之前，发送到带验证器的请求到原始服务器
- only-if-cached： 客户端指令，表明客户端只接受已缓存的响应，并且不要向原始服务器检查是否有更新的拷贝
### 到期
- max-age=<seconds> 设置缓存存储的最大周期，超过这个时间缓存被认为过期（单位：秒）
- s-maxage=<seconds> 服务端指令，覆盖max-age 或者 Expires 头，但是仅适用于共享缓存(比如各个代理)，并且私有缓存中它被忽略
- max-stale[=<seconds>] 客户端指令，表明客户端愿意接收一个已经过期的资源。 可选的设置一个时间(单位秒)，表示响应不能超过的过时时间
- min-fresh=<seconds> 客户端指令，表示客户端希望在指定的时间内获取最新的响应
### 重新验证和重新加载
- must-revalidate： 服务端指令，缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源
- proxy-revalidate： 服务端指令，与must-revalidate作用相同，但它仅适用于共享缓存（例如代理），并被私有缓存忽略
### 其他
- no-store： 缓存不应存储有关客户端请求或服务器响应的任何内容，即禁用缓存
- no-transform： 服务端指令，不得对资源进行转换或转变。Content-Encoding, Content-Range, Content-Type等HTTP头不能由代理修改。例如，非透明代理可以对图像格式进行转换，以便节省缓存空间或者减少缓慢链路上的流量。 no-transform指令不允许这样做

[slide]
## 主页面样式
### ----是上下分界线
----

nodeppt是基于nodejs写的支持 **Markdown!** 语法的网页PPT，当前版本：1.4.5

Github：https://github.com/ksky521/nodeppt