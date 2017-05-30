### 关键词

#### Cache-Control

[`Cache-Control 标头`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)控制浏览器和其他中间缓存如何缓存各个响应以及缓存多久，常用指令如下：

`no-cache`: 每次在使用缓存资源之前，都必须向服务器请求并验证。

`no-store`: 不允许缓存任何服务器响应或客户端请求。

`public`: 响应可以被任何对象缓存。

`private`: 响应只能被单个用户缓存，不允许任何中间缓存（代理服务器、CDN等）对其进行缓存。

`max-age`: 允许响应被缓存的最长时间（秒）。

在接下来的60秒内重复请求资源将使用缓存数据，不会向服务器发送请求。
```
Cache-Control: max-age=60
```

`max-stale`: 缓存过期后，在指定的时间内（秒）客户端仍然接受该缓存。参考 [max-age 与 max-stale 区别](http://stackoverflow.com/questions/33818854/what-is-difference-between-max-age-and-max-stale-in-cache-control-mechanism)

`must-revalidate`: 在缓存过期之后，再次请求资源必须向服务器验证旧资源的状态，即使被告知旧资源是可用的（比如 max-stale 告知过期缓存继续可用）。

#### If-Modified-Since/Last-Modified

服务器响应头中的 Last-Modified 标头纪录了资源在服务端最后被修改的时间，当客户端再请求该资源时会带上 If-Modified-Since 标头，提供之前的 Last-Modified 值供服务器校验，如果资源没有改动，则返回'304 Not Modified'。

#### If-None-Match/ETag

服务端在对资源请求进行响应时可以在响应头里带上 ETag 值，通常是文件的哈希值或某个其他指纹，客户端接收到 ETag 值之后将在后续的请求中通过 If-None-Match 请求标头提供 ETag 指纹，如果未发生变化，服务器将返回304状态码和一个空的 body。如果 Cache-Control 中设有 max-age，缓存将会重新计算 age。

### 疑问

1. _Cache-Control: max-age=0 与 Cache-Control: no-cache 区别？_

Cache-Control: max-age=0 表示响应可以被重用的最长时间为0秒，下一次请求时**应该**去服务端请求新的资源，不过某些时候客户端也会愿意接受一个已经过期的资源，这时就不会向服务端发送请求。Cache-Control: no-cache 并不是不缓存响应，而是表示使用缓存的数据前，无论何时**必须**向服务器发送请求进行验证。

基于这种分析可以推测 Cache-Control: max-age=0, must-revalidate 与 Cache-Control: no-cache 行为类似

2. _If-Modified-Since 与 If-None-Match 优先级？_

当 If-Modified-Since 和 If-None-Match 同时存在时，将会忽略 If-Modified-Since，相对于 If-Modified-Since，If-None-Match 有着更精准的判断条件。二者同时使用仅仅为了兼容不支持 If-None-Match 的旧环境。

3. _max-age 与 Expires 优先级？_

Cache-Control 标头是在 HTTP/1.1 规范中定义的，Cache-Control: max-age 会被优先解析。

4. _客户端请求头 Cache-Control 作用？_

请求头中的 Cache-Control 可以影响中间代理的缓存策略，比如当请求带有 Cache-Control: no-cache 时，中间代理将向服务器重新发送验证信息，即使有未过期的缓存资源也不会直接返回给请求方。

### 参考

- [https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)
- [https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ)
- [https://www.mnot.net/cache_docs/#BROWSER](https://www.mnot.net/cache_docs/#BROWSER)
