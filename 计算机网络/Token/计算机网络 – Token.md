## 计算机网络 -- Token

@(Interview)

### 前言

`token` 是除了 `Cookie`和`Session`以外的另一种身份验证的方式，它的出现解决了很多问题，比如 `CSRF`，还有`session`分布式部署的问题。

`Cookie` 和 `Session` 这一套验证机制还是有一些问题的：

1. `cookie` + `session` 在跨域场景表现并不好
2. 如果是分布式部署，需要做多机共享`session`机制，实现方法可将 `session` 存储到数据库中或者 `redis` 中
3. 基于 `cookie` 的机制很容易被 CSRF攻击
4. 查询 `session` 信息可能会有数据库查询操作

而`token`的出现，解决了大部分问题，细细讲一下`token`

### 一丶Token是什么

`Token` 的中文有人翻译成 “令牌”，我觉得挺好，意思就是，你拿着这个令牌，才能过一些关卡。

我们常说的`Token`其实是一种安全验证机制。

看一下`token`请求的过程

1. 客户端使用用户名跟密码请求登录
2. 服务端收到请求，去验证用户名与密码
3. 验证成功后，服务端会签发一个 Token，再把这个 Token 发送给客户端
4. 客户端收到 Token 以后可以把它存储起来，比如放在 Cookie 里或者 Local Storage 里
5. 客户端每次向服务端请求资源的时候需要带着服务端签发的 Token
6. 服务端收到请求，然后去验证客户端请求里面带着的 Token，如果验证成功，就向客户端返回请求的数据

这就是`token`的原理，看着是不是和`Cookie`，`Session`那一套一样，其实还是有很大区别的，看下面

### 二丶Token的原理

服务器对于`session ID`保存在后端是一件很苦恼的事情，因为这样的操作会让服务器的运算量变的很大，有可能造成性能低下，甚至如果服务器挂了，那`session ID`将不复存在。

有这样一天，服务器想到了一个好办法，让客户端拿着这个`session ID`，来我这验证，为了防止被篡改，我们对数据做一个签名。

比如说用`HMAC-SHA256`算法，加上一个只有我才知道的**密钥**，对数据做一个签名，然后把这个签名和数据一起作为`token`发送给客户端，这样的话，没人知道**密钥**是什么，也就无法篡改和伪造了。

![Alt text](./token1.png)

通过图可以看到，我们把用户的信息和一个叫做`Header`的一起通过算法加密，**密钥保存在服务端**，生成了一个签名，**再把这个签名和数据一起发送给前端。**

这样服务器就不会保存数据了。

当客户端下次再来找服务器请求数据的时候，先来验证这个`Token`，验证通过了才让客户端去拿数据。这个验证具体是怎么样的呢

很简单，既然你拿着的**既有加密前（用户信息和`Header`）的信息，还有加密后（签名）的信息**，服务器只需要**用同样的算法和同样的密钥**再把这些信息加密一遍，**然后和带来的签名进行对比**，如果相同，就可以通过，如果不相同，说明被人篡改了，不会让你进的。

![Alt text](./token2.png)

这样就可以很安全的进行认证了。


### 三丶Token的使用

实现`Token`的方法有很多，今天我们着重介绍`JWT`

#### 1. JWT

`JWT`称为`JSON WEB TOKEN`，是一个对于`Token`的实现


#### 2. JWT的结构

`JWT` 主要由三个部分组成，使用`.`号连接：

分别是`header`，`payload`，`signature`，前两部分都会使用`Base64`编码，所以真正的`Token`看起来是这样的

> `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJuaW5naGFvLm5ldCIsImV4cCI6IjE0Mzg5NTU0NDUiLCJuYW1lIjoid2FuZ2hhbyIsImFkbWluIjp0cnVlfQ.SwyHTEx_RQppr97g4J5lKXtabJecpejuef8AqKYMAJc`

##### (1). Header

`header`是头部，这个部分主要是两部分内容，一个是**`Token`的类型**，另一个是**使用的算法**，比如下面类型就是`JWT`，使用的算法是`HS256`

```
{
  "typ": "JWT",
  "alg": "HS256"
}
```

上面的内容使用了`Base64`的形式编码一下，就变成了下面这样

> `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`

##### (2). Payload

`Payload` 被称为 **有效负载**，这部分里面是 `Token` 的具体内容，这些内容里面有一些是标准字段，你也可以添加其它需要的内容。下面是标准字段：

- `iss`：Issuer，发行者
- `sub`：Subject，主题
- `aud`：Audience，观众
- `exp`：Expiration time，过期时间
- `nbf`：Not before
- `iat`：Issued at，发行时间
- `jti`：JWT ID

比如下面这个 `Payload`，用到了 `iss` 发行人，还有`exp`过期时间。另外还有两个自定义的字段，一个是`name`，还有一个是`admin`

```
{
 "iss": "ninghao.net",
 "exp": "1438955445",
 "name": "wanghao",
 "admin": true
}
```
使用 Base64 编码以后就变成了这个样子：
> `eyJpc3MiOiJuaW5naGFvLm5ldCIsImV4cCI6IjE0Mzg5NTU0NDUiLCJuYW1lIjoid2FuZ2hhbyIsImFkbWluIjp0cnVlfQ`

##### (3). Signature

`JWT`的最后一部分是 `Signature`签名，这部分内容有三个部分，分别是前面的`header`，`payload`，还有一个`secret`

用加密算法加密用`Base64`编码过的`header.payload`，加密的时候放进去一个 `Secret`，这个相当于是一个密码，这个密码秘密的存储在服务端，过程如下

```
let encodedString = `${base64UrlEncode(header)}.${base64UrlEncode(payload)}`; 
HMACSHA256(encodedString, 'secret');
```

处理完成以后看起来像这样：
> `SwyHTEx_RQppr97g4J5lKXtabJecpejuef8AqKYMAJc`

最后把三部分组合起来，就是完整的一个`Token`

> `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJuaW5naGFvLm5ldCIsImV4cCI6IjE0Mzg5NTU0NDUiLCJuYW1lIjoid2FuZ2hhbyIsImFkbWluIjp0cnVlfQ.SwyHTEx_RQppr97g4J5lKXtabJecpejuef8AqKYMAJc`

服务器生成这个以后把他发送给客户端，客户端拿到后储存在`localStorage`，下次访问再携带，一般是通过字段`Authorization`，这个字段保存的就是`token`

> `Authorization: Bearer <token>`

### 四丶Token 的一些问题

#### 1. 有效期问题

`token`在平时验证的时候，肯定是会有过期时间的，但是这个过期时间设置多少是一个问题，**根据系统的安全需要**，这个值应该尽可能的短，但是如果说用户在正常操作的时候，`token`突然过期了，然后要求用户重新登录，那这就是一个令人很头疼的问题，用户体验非常低。

为了解决在操作过程中不能让用户感到 `token` 失效这个问题，有一种解决方案是在服务器保存 `token`状态，用户每次操作都会自动刷新（推迟） `Token` 的过期时间——`Session` 就是采用这种策略来保持用户登录状态的。然而仍然存在这样一个问题，在前后端分离、单页 App 这些情况下，每秒种可能发起很多次请求，每次都去刷新过期时间会**产生非常大的代价**。如果 `Token` 的过期时间被持久化到数据库或文件，代价就更大了。

还有另一种方案，使用 `Refresh Token`，它可以避免频繁的读写操作。

这种方案中，服务端不需要刷新 `Token` 的过期时间，一旦 `Token` 过期，就反馈给前端，前端使用 `Refresh Token` 申请一个全新 `Token` 继续使用。这种方案中，服务端只需要在客户端请求更新 `Token` 的时候对 `Refresh Token` 的有效性进行一次检查，大大减少了更新有效期的操作，也就避免了频繁读写。当然 `Refresh Token` 也是有有效期的，但是这个有效期就可以长一点了，比如，以天为单位的时间。这个方案，**就是设置了两个`Token`**，一个用来验证登录状态，一个用来请求下一个`Token`，流程如下

![Alt text](./token3.png)

![Alt text](./token4.png)

![Alt text](./token5.png)

那有朋友会问了，`Refresh Token`过期了怎么办，这就看自己了，可以有两种方法
- `Refresh Token`过期，用户重新登录
- `Refresh Token`在每次使用的时候都更新它的过期时间，`Refresh Token`的使用周期肯定是要比`token`的使用周期长的，所以不会造成频繁读写的问题了

#### 2. 注销问题

虽然`token`不需要保存信息，但是还是需要保存一个未到期却已注销的`Token`，因为如果一个`token`未到期却被用户主动注销，那么服务器需要保存这个被注销的 `token`，以便下次收到使用这个仍在有效期内的`token`时判其无效。

但是这也是分情况的，如果在前端可控的情况下（比如前端和服务端在同一个项目组内），可以协商：前端一但注销成功，就丢掉本地保存（比如保存在内存、LocalStorage 等）的 `Token` 和 `Refresh Token`。基于这样的约定，服务器就可以假设收到的 `Token` 一定是没注销的（因为注销之后前端就不会再使用了）

如果前端不可控的情况，仍然可以进行上面的假设，但是这种情况下，需要尽量缩短 `Token` 的有效期，而且必须在用户主动注销的情况下让 `Refresh Token` 无效。这个操作存在一定的安全漏洞，因为用户会认为已经注销了，实际上在较短的一段时间内并没有注销。如果应用设计中，这点漏洞并不会造成什么损失，那采用这种策略就是可行的。


### 五丶Token的优点

相比较于 `session`，`cookie`那一套，`token`的优点有以下的：

1. Token 完全由应用管理，所以它可以避开同源策略
2. Token 可以避免 CSRF 攻击
3. Token 可以是无状态的，可以在多个服务间共享
4. Token 可以解决服务器负载过高的问题
5. Token 的验证速度快很多

---

##### 参考：
- <a href="https://juejin.im/entry/5a542884f265da3e4a6f560e">https://juejin.im/entry/5a542884f265da3e4a6f560e</a>
- <a href="https://juejin.im/entry/592e286d44d9040064592a7b">https://juejin.im/entry/592e286d44d9040064592a7b</a>
- <a href="https://juejin.im/post/5a437441f265da43294e54c3">https://juejin.im/post/5a437441f265da43294e54c3</a>
- <a href="https://juejin.im/post/5a6c60166fb9a01caf37a5e5">https://juejin.im/post/5a6c60166fb9a01caf37a5e5</a>

---
