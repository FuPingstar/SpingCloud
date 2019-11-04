#### HTTP COOKIES

##### 什么是Cookie

+ cookie是浏览器存储在用户电脑上的一段文本文件。cookie是纯文本格式，不包含任何可以执行的代码，所以说cookie本身并不有害。一个web页面或者服务器告知浏览器按照一定的规范来存储这些信息，并在随后的请求中将这些信息发送至服务器，Web服务器就可以使用这些信息来识别不同的用户，大多数需要登录的网站在用户成功验证之后都会设置一个cookie，只要这个cookie存在并可以，用户就可以自由的浏览这个网站的任意页面。

#### 创建cookie

+ web服务器发送一个Set-Cookie的http消息头来创建一个cookie，Set-Cookie消息头是一个字符串，格式如下：

  ```java
  	Set-Cookie: value[; expires=date][; domain=domain][; path=path][; secure]
  	第一部分：value部分，是一个name=value格式的字符串，浏览器并不会对cookie值按照此格式来验证。
  	当存在一个Cookie时，并允许设置可选项，该cookie的值会在随后的每次请求中被发送至服务器，cookie的值被存储在名为Cookie的http 消息头中，并且只包含了cookie的值，忽略全部设置选项。
  	通过Set-Cookie指定的可选项只会在浏览器端使用，不会被发送至服务器端，发送至服务器的cookie的值与通过Set-Cookie指定的值完全一样，不会有进一步的解析和转码操作。如果请求中包含多个cookie，将会被分号和空格隔开。
  	紧跟cookie值后面的每个选项都以分号和空格隔开，每个选择都指定了cookie在什么情况下应该被发送至服务器。
  	
  ```

#### cookie编码

+ 对于 cookie 的值进行编码一直都存在一些困惑。普遍认为 cookie 的值必须经过 URL 编码，但其实这是一个谬论，尽管通常都这么做。__原始规范中明确指出只有三个字符必须进行编码：分号、逗号和空格，规范中还提到可以进行 URL 编码，但并不是必须，在 RFC 中没有提及任何编码__。然而，几乎所有的实现都对 cookie 的值进行了一系列的 URL 编码。对于 `name=value` 格式，通常会对 `name` 和 `value` 分别进行编码，而不对等号 `=` 进行编码操作。

#### Cookie选项

1. 过期时间选项

   ```java
   Set-Cookie: name=Nicholas; expires=Sat, 02 May 2009 23:38:25 GMT
   -- 过期时间选项（expires）指定了cookie何时不会再被发送至服务器，随后浏览器将删除该cookie。
   -- 没有设置 expires 选项时，cookie 的生命周期仅限于当前会话中，关闭浏览器意味着这次会话的结束，所以会话 cookie 仅存在于浏览器打开状态之下。这就是为什么为什么当你登录一个 Web 应用时经常会看到一个复选框，询问你是否记住登录信息：如果你勾选了复选框，那么一个 expires 选项会被附加到登录 cookie 中。如果 expires 设置了一个过去的时间点，那么这个 cookie 会被立即删掉。
   ```

2. domain选项

   ```java
   Set-Cookie: name=Nicholas; domain=nczonline.net
   -- domain选项指定了cookie将要被发送至哪个或者哪些域中，默认情况下，domian会被设置为创建该cookie的页面所在的域名，所以当给相同域名发送请求时该cookie会被发送至服务器，
   ```

3. path选项

   ```java
   Set-Cookie:name=Nicholas;path=/blog
   -- path选项指定了请求的资源URL中必须存在指定的路径时，才会发送cookie消息头，这个比较通常是将 path 选项的值与请求的 URL 从头开始逐字符比较完成的。如果字符匹配，则发送 Cookie 消息头
   -- 注意： 只有在domain选项何时完毕之后才会path属性进行比较。path属性的默认值是发送set-cookie消息头所对应的URl中的path部分。
   ```

4. secure选项

   ```java
   Set-Cookie: name=Nicholas; secure
   -- 不像其它选项，该选项只是一个标记而没有值。只有当一个请求通过 SSL 或 HTTPS 创建时，包含 secure 选项的 cookie 才能被发送至服务器。这种 cookie 的内容具有很高的价值，如果以纯文本形式传递很有可能被篡改
   -- 事实上，机密且敏感的信息绝不应该在 cookie 中存储或传输，因为 cookie 的整个机制原本都是不安全的。默认情况下，在 HTTPS 链接上传输的 cookie 都会被自动添加上 secure 选项
   ```

   

#### Cookie的维护和生命周期

__在一个 cookie 中可以指定任意数量的选项，并且这些选项可以是任意顺序__

```java
	
Set-Cookie:name=Nicholas; domain=nczonline.net; path=/blog
-- 这个 cookie 有四个标识符：cookie 的 name，domain，path，secure 标记。要想改变这个 cookie 的值，需要发送另一个具有相同 cookie name，domain，path 的 Set-Cookie 消息头。
例如： 	
Set-Cookie: name=Greg; domain=nczonline.net; path=/blog
但是，修改 cookie 选项的任意一项都将创建一个完全不同的新 cookie
```



1. 使用失效日期
2. cookie自动删除
   + 会话 cooke (Session cookie) 在会话结束时（浏览器关闭）会被删除
   + 持久化 cookie（Persistent cookie）在到达失效日期时会被删除
   + 如果浏览器中的 cookie 数量达到限制，那么 cookie 会被删除以为新建的 cookie 创建空间。
3. cookie限制条件
   + cookie的属性
   + cookie的总大小： 原始规范中限定每个域名下不超过20个cookie，后续各浏览器都有提高，发现服务器中的所有cookie的对打数量维持原始规范中所指出的：4KB。

