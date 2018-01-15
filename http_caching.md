title: 缓存和cdn
speaker: stanxing
url: https://github.com/ksky521/nodeppt
transition: side2

[slide]
# 缓存和CDN
## 演讲者：stanxing

[slide]
## 缓存类型
- 私有缓存： 浏览器缓存，只能被一个用户使用
- 共享缓存： CDN，可以被多个用户使用
[slide]
## 缓存控制类型
- 强制缓存（用来判断缓存是否过期）
    - Cache-control http/1.1
    - Expires http/1.0
- 协商缓存（用来缓存校验，客户端缓存于服务端资源文件是否一致）
    - Last-Modified/If-Modified-Since
    - Etag/If-None-Match
[slide]
## 普遍的缓存操作的目标
- 一个检索请求的成功响应：一般只能存储对GET请求的响应，对于GET请求，响应状态码为200
- 不变的重定向： 301
- 错误响应： 404的一个页面
- 不完全的响应： 状态码206，只返回局部的信息
- 除了 GET 请求外，如果匹配到作为一个已被定义的cache键名的响应
[slide]
## Cache-control 头

- http/1.1定义的Cache-control头用来区分对缓存机制的支持情况，请求头和响应头都支持这个属性
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
## Cache-control 头
### 可缓存性
- public： 服务端指令，表明响应可以被任何对象缓存（包括发送请求的客户端，中间的代理服务器）
- private： 服务端指令，表明响应只能被单个用户缓存（即中间的代理服务器不能缓存它）
- no-cache： 强制所有缓存了该响应的缓存用户，在使用已存储的缓存数据之前，发送到带验证器的请求到原始服务器
### 到期
- max-age=<seconds> 设置缓存存储的最大周期，超过这个时间缓存被认为过期（单位：秒）
- s-maxage=<seconds> 服务端指令，覆盖max-age 或者 Expires 头，但是仅适用于共享缓存(比如各个代理)，并且私有缓存中它被忽略
### 重新验证和重新加载
- must-revalidate： 服务端指令，缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源
- proxy-revalidate： 服务端指令，与must-revalidate作用相同，但它仅适用于共享缓存（例如代理），并被私有缓存忽略
### 其他
- no-store： 缓存不应存储有关客户端请求或服务器响应的任何内容，即禁用缓存
- no-transform： 服务端指令，不得对资源进行转换或转变。Content-Encoding, Content-Range, Content-Type等HTTP头不能由代理修改。例如，非透明代理可以对图像格式进行转换，以便节省缓存空间或者减少缓慢链路上的流量。 no-transform指令不允许这样做
[slide]
## 缓存过程
- 当客户端第一次访问服务器的时候，浏览器会缓存接收到的资源，缓存的过期时间存在响应头中，比如 Expires， Cache-control， 
- 当客户端在缓存过期时间内再次访问，浏览器会首先去缓存内寻找，当缓存命中而且缓存在过期时间内，浏览器会直接返回缓存结果，响应码 200。
- 当客户端在过期时间后发起请求，浏览器依然会在缓存内寻找，命中缓存但发现缓存过期，浏览器会附加一个条件请求首部，（If-Modified-Since 或者If-None-Match，根据缓存的响应头确定），如果服务器通过请求首部判断资源没有改变，会返回响应码 304 （该响应不会有实体信息）；如果资源发生了变化，则会返回新的资源，响应码 200。
[slide]
# 缓存寿命的计算
- 对于Cache-control: max-age=N的请求头，相应的缓存的寿命就是N，N是一个数值。
- 对于不含这个属性的请求则会去查看是否包含Expires属性，通过比较Expires的值和请求头里面Date属性的值来判断是否缓存还有效。（Expires的值是一个日期）
- 如果max-age和expires属性都没有，找头里的Last-Modified信息（该属性的值也是一个日期）。如果有，缓存的寿命就等于头里面Date的值减去Last-Modified的值乘以 0.1。假设缓存中的响应头为 date：Sun, 14 Jan 2018 10:04:12 GMT ，Last-Modified： Sun, 14 Jan 2018 17:04:12 GMT，那么该缓存的寿命为 7h x 3600s/h x 0.1 = 2520s, 在该时间内缓存会直接返回结果
[slide]
# 缓存校验
## Last-Modified/If-Modified-Since
- Last-Modified 是一个响应首部，其中包含源头服务器认定的资源做出修改的日期及时间。它通常被用作一个验证器来判断接收到的或者存储的资源是否彼此一致。由于精确度比 ETag 要低，所以这是一个备用机制。
- If-Modified-Since 是一个条件式请求首部，服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200。
- 如果请求的资源从那时起未经修改，那么返回一个不带有消息主体的 304 响应，而在 Last-Modified 首部中会带有上次修改时间。If-Modified-Since 只可以用在 GET 或 HEAD 请求中。
[slide]
# 缓存校验
## Etag/If-None-Match
- ETag 是一个响应首部，是资源的特定版本的标识符，当资源更新更新时，一定要返回一个新的ETag值，它也是被用来作为一个验证器去判断接收到的资源是否是最新的，它的精确度比 Last-Modified 要高。
- If-None-Match 是一个条件式请求首部。对于 GET 和 HEAD 请求方法来说，当且仅当服务器上没有任何资源的 ETag 属性值与这个首部中列出的相匹配的时候，服务器端会才返回所请求的资源，响应码为 200。
- 对于 GET 和 HEAD 方法来说，当验证失败的时候，服务器端必须返回响应码 304 （Not Modified，未改变）
- 对于能够引发服务器状态改变的方法（比如 POST ），则返回 412 （Precondition Failed，前置条件失败）
- 当与 If-Modified-Since 一同使用的时候，If-None-Match 优先级更高
[slide]
# 阿里云CDN的缓存策略

![aliyun_cdn](/images/cache.png "cache")
[slide]
# CDN缓存的更新方法
- CDN中对于更新不频繁的静态文件，最好的方式是尽可能的设置长的缓存时间，这样可以提高性能；但是这样存在一个问题就是，当资源需要更新的时候就变得比较困难。
- 刷新CDN是一种更新缓存的方式，可以让CDN回源站重新更新缓存
- 另一种方式是在URL后面（通常是文件名后面）会加上版本号，加上版本号后会被视为一个新的资源，客户端访问时发现CDN中不存在，CDN就会回源去拉取新的资源，这样就解决了频繁刷新的问题
- 但是这样做需要更新资源所被引用的入口文件，一般是HTML（HTML入口文件一般不用做缓存），这一般是通过自动化工具来完成的，比如说glup