---
title: OAuth2
date: 2024-10-15 16:14:25.000 +0800
type: book
---

## 简介

OAuth2 是一个开放授权协议，它允许用户授权第三方应用访问用户数据，而无需将用户名和密码提供给第三方应用。

## 核心概念

OAuth 2.0 主要有 4 类角色：

1. resource owner：资源所有者，指终端的“用户”（user）
2. resource server：资源服务器，即服务提供商存放受保护资源。访问这些资源，需要获得访问令牌（access token）。
3. client：客户端，代表向受保护资源进行资源请求的第三方应用程序。
4. authorization server： 授权服务器， 在验证资源所有者并获得授权成功后，将发放访问令牌给客户端。

## 协议流程

### 一般流程：流程的关键信息及步骤

    +--------+                               +---------------+
    |        |--(A)- Authorization Request ->|   Resource    |
    |        |                               |     Owner     |
    |        |<-(B)-- Authorization Grant ---|               |
    |        |                               +---------------+
    |        |
    |        |                               +---------------+
    |        |--(C)-- Authorization Grant -->| Authorization |
    | Client |                               |     Server    |
    |        |<-(D)----- Access Token -------|               |
    |        |                               +---------------+
    |        |
    |        |                               +---------------+
    |        |--(E)----- Access Token ------>|    Resource   |
    |        |                               |     Server    |
    |        |<-(F)--- Protected Resource ---|               |
    +--------+                               +---------------+

> （A）用户打开客户端以后，客户端要求用户给予授权。  
> （B）用户同意给予客户端授权。  
> （C）客户端使用上一步获得的授权，向认证服务器申请令牌。  
> （D）认证服务器对客户端进行认证以后，确认无误，同意发放令牌。  
> （E）客户端使用令牌，向资源服务器申请获取资源。  
> （F）资源服务器确认令牌无误，同意向客户端开放资源。

### 刷新 token：在以上步骤中添加刷新 token 步骤

    +--------+                                           +---------------+
    |        |--(A)------- Authorization Grant --------->|               |
    |        |                                           |               |
    |        |<-(B)----------- Access Token -------------|               |
    |        |               & Refresh Token             |               |
    |        |                                           |               |
    |        |                            +----------+   |               |
    |        |--(C)---- Access Token ---->|          |   |               |
    |        |                            |          |   |               |
    |        |<-(D)- Protected Resource --| Resource |   | Authorization |
    | Client |                            |  Server  |   |     Server    |
    |        |--(E)---- Access Token ---->|          |   |               |
    |        |                            |          |   |               |
    |        |<-(F)- Invalid Token Error -|          |   |               |
    |        |                            +----------+   |               |
    |        |                                           |               |
    |        |--(G)----------- Refresh Token ----------->|               |
    |        |                                           |               |
    |        |<-(H)----------- Access Token -------------|               |
    +--------+           & Optional Refresh Token        +---------------+

> （A） 客户端通过向授权服务器进行身份验证并提供授权来请求访问令牌。  
> （B） 授权服务器对客户端进行身份验证并验证授权授予，如果有效，则颁发访问令牌和刷新令牌。  
> （C） 客户端通过提供访问令牌向资源服务器发出受保护的资源请求。  
> （D） 资源服务器验证访问令牌，如果有效，则为请求提供服务。  
> （E） 重复步骤 （C） 和 （D），直到访问令牌过期。 如果客户端知道访问令牌过期，它会跳到步骤
> （G）;否则，它将发出另一个受保护的资源请求。  
> （F） 由于访问 Token 无效，资源服务器返回 invalid token 错误。  
> （G） 客户端通过向授权服务器进行身份验证并提供刷新令牌来请求新的访问令牌。
> 客户端身份验证要求基于客户端类型和授权服务器策略。  
> （H） 授权服务器对客户端进行身份验证并验证刷新令牌，如果有效，则颁发新的访问令牌（以及可选的新刷新令牌）。

## 授权模式：具体的授权流程

- 授权码模式（authorization code）
- 简化模式（implicit）
- 密码模式（resource owner password credentials）
- 客户端模式（client credentials）

### 授权码模式（Authorization Code Grant）

授权码模式是功能最完整、流程最严密的授权模式。它的特点就是通过客户端的后台服务器，与"服务提供商"的认证服务器进行互动。

     +----------+
     | Resource |
     |   Owner  |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier      +---------------+
     |         -+----(A)-- & Redirection URI ---->|               |
     |  User-   |                                 | Authorization |
     |  Agent  -+----(B)-- User authenticates --->|     Server    |
     |          |                                 |               |
     |         -+----(C)-- Authorization Code ---<|               |
     +-|----|---+                                 +---------------+
       |    |                                         ^      v
      (A)  (C)                                        |      |
       |    |                                         |      |
       ^    v                                         |      |
     +---------+                                      |      |
     |         |>---(D)-- Authorization Code ---------'      |
     |  Client |          & Redirection URI                  |
     |         |                                             |
     |         |<---(E)----- Access Token -------------------'
     +---------+       (w/ Optional Refresh Token)

> （A） 客户端通过将资源所有者的用户代理定向到授权终端节点来启动流。 客户端包括其客户端标识符、请求范围、本地状态和重定向
> URI，一旦授予（或拒绝）访问权限，授权服务器会将用户代理发回该 URI。  
> （B） 授权服务器对资源所有者进行 身份验证（通过用户代理），并确定资源所有者是授予还是拒绝客户端的访问请求。  
> （C） 假设资源所有者授予访问权限，授权服务器使用之前提供的重定向 URI（在请求中或在客户端注册期间）将用户代理重定向回客户端。
> 重定向 URI 包括授权代码和客户端之前提供的任何本地状态。  
> （D） 客户端通过包含上一步中收到的授权代码，从授权服务器的令牌端点请求访问令牌。 发出请求时，客户端向授权服务器进行身份验证。
> 客户端包含用于获取授权码的重定向 URI 以进行验证。  
> （E） 授权服务器对客户端进行身份验证，验证授权代码，并确保收到的重定向 URI 与步骤 （C） 中用于重定向客户端的 URI 匹配。
> 如果有效，授权服务器将使用访问令牌和刷新令牌（可选）进行响应。

A 步骤中，客户端申请认证的 URI，包含以下参数：

response_type：表示授权类型，必选项，此处的值固定为"code"
client_id：表示客户端的 ID，必选项
redirect_uri：表示重定向 URI，可选项
scope：表示申请的权限范围，可选项
state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。
下面是一个例子。

> GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
> &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
> Host: server.example.com

C 步骤中，服务器回应客户端的 URI，包含以下参数：

code：表示授权码，必选项。该码的有效期应该很短，通常设为 10 分钟，客户端只能使用该码一次，否则会被授权服务器拒绝。该码与客户端
ID 和重定向 URI，是一一对应关系。
state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数。
下面是一个例子。

> HTTP/1.1 302 Found  
> Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz

D 步骤中，客户端向认证服务器申请令牌的 HTTP 请求，包含以下参数：

grant_type：表示使用的授权模式，必选项，此处的值固定为"authorization_code"。
code：表示上一步获得的授权码，必选项。
redirect_uri：表示重定向 URI，必选项，且必须与 A 步骤中的该参数值保持一致。
client_id：表示客户端 ID，必选项。
下面是一个例子。

    POST /token HTTP/1.1  
    Host: server.example.com  
    Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW  
    Content-Type: application/x-www-form-urlencoded

    grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb

E 步骤中，认证服务器发送的 HTTP 回复，包含以下参数：

access_token：表示访问令牌，必选项。
token_type：表示令牌类型，该值大小写不敏感，必选项，可以是 bearer 类型或 mac 类型。
expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。
refresh_token：表示更新令牌，用来获取下一次的访问令牌，可选项。
scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。
下面是一个例子。

    HTTP/1.1 200 OK  
    Content-Type: application/json;charset=UTF-8  
    Cache-Control: no-store  
    Pragma: no-cache
    {  
      "access_token":"2YotnFZFEjr1zCsicMWpAA",  
      "token_type":"example",  
      "expires_in":3600,  
      "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",  
      "example_parameter":"example_value"  
    }

从上面代码可以看到，相关参数使用 JSON 格式发送（Content-Type: application/json）。此外，HTTP 头信息中明确指定不得缓存。

### 简化模式（Implicit Grant）

简化模式（implicit grant type）不通过第三方应用程序的服务器，直接在浏览器中向认证服务器申请令牌，跳过了"授权码"
这个步骤，因此得名。所有步骤在浏览器中完成，令牌对访问者是可见的，且客户端不需要认证。

     +----------+
     | Resource |
     |  Owner   |
     |          |
     +----------+
          ^
          |
         (B)
     +----|-----+          Client Identifier     +---------------+
     |         -+----(A)-- & Redirection URI --->|               |
     |  User-   |                                | Authorization |
     |  Agent  -|----(B)-- User authenticates -->|     Server    |
     |          |                                |               |
     |          |<---(C)--- Redirection URI ----<|               |
     |          |          with Access Token     +---------------+
     |          |            in Fragment
     |          |                                +---------------+
     |          |----(D)--- Redirection URI ---->|   Web-Hosted  |
     |          |          without Fragment      |     Client    |
     |          |                                |    Resource   |
     |     (F)  |<---(E)------- Script ---------<|               |
     |          |                                +---------------+
     +-|--------+
       |    |
      (A)  (G) Access Token
       |    |
       ^    v
     +---------+
     |         |
     |  Client |
     |         |
     +---------+


> （A）客户端将用户导向认证服务器。  
> （B）用户决定是否给于客户端授权。  
> （C）假设用户给予授权，认证服务器将用户导向客户端指定的"重定向 URI"，并在 URI 的 Hash 部分包含了访问令牌。  
> （D）浏览器向资源服务器发出请求，其中不包括上一步收到的 Hash 值。  
> （E）资源服务器返回一个网页，其中包含的代码可以获取 Hash 值中的令牌。  
> （F）浏览器执行上一步获得的脚本，提取出令牌。  
> （G）浏览器将令牌发给客户端。

下面是上面这些步骤所需要的参数。

A 步骤中，客户端发出的 HTTP 请求，包含以下参数：

response_type：表示授权类型，此处的值固定为"token"，必选项。
client_id：表示客户端的 ID，必选项。
redirect_uri：表示重定向的 URI，可选项。
scope：表示权限范围，可选项。
state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。
下面是一个例子。

    GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
    Host: server.example.com

C 步骤中，认证服务器回应客户端的 URI，包含以下参数：

- access_token：表示访问令牌，必选项。
- token_type：表示令牌类型，该值大小写不敏感，必选项。
- expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。
- scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。
- state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数。

下面是一个例子。

    HTTP/1.1 302 Found
    Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA&state=xyz&token_type=example&expires_in=3600

在上面的例子中，认证服务器用 HTTP 头信息的 Location 栏，指定浏览器重定向的网址。注意，在这个网址的 Hash 部分包含了令牌。

根据上面的 D 步骤，下一步浏览器会访问 Location 指定的网址，但是 Hash 部分不会发送。接下来的 E 步骤，服务提供商的资源服务器发送过来的代码，会提取出
Hash 中的令牌。

### 密码模式（Resource Owner Password Credentials Grant）

密码模式（Resource Owner Password Credentials Grant）中，用户向客户端提供自己的用户名和密码。客户端使用这些信息，向"
服务商提供商"索要授权。  
在这种模式中，用户必须把自己的密码给客户端，但是客户端不得储存密码。这通常用在用户对客户端高度信任的情况下，比如客户端是操作系统的一部分，或者由一个著名公司出品。而认证服务器只有在其他授权模式无法执行的情况下，才能考虑使用这种模式。

     +----------+
     | Resource |
     |  Owner   |
     |          |
     +----------+
          v
          |    Resource Owner
         (A) Password Credentials
          |
          v
     +---------+                                  +---------------+
     |         |>--(B)---- Resource Owner ------->|               |
     |         |         Password Credentials     | Authorization |
     | Client  |                                  |     Server    |
     |         |<--(C)---- Access Token ---------<|               |
     |         |    (w/ Optional Refresh Token)   |               |
     +---------+                                  +---------------+

它的步骤如下：

> （A）用户向客户端提供用户名和密码。  
> （B）客户端将用户名和密码发给认证服务器，向后者请求令牌。  
> （C）认证服务器确认无误后，向客户端提供访问令牌。

B步骤中，客户端发出的HTTP请求，包含以下参数：

grant_type：表示授权类型，此处的值固定为"password"，必选项。
username：表示用户名，必选项。
password：表示用户的密码，必选项。
scope：表示权限范围，可选项。
下面是一个例子。

     POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=password&username=johndoe&password=A3ddj3w

C步骤中，认证服务器向客户端发送访问令牌，下面是一个例子。

     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }

上面代码中，各个参数的含义参见《授权码模式》一节。

整个过程中，客户端不得保存用户的密码。

### 客户端模式（Client Credentials Grant）

客户端模式（Client Credentials Grant）指客户端以自己的名义，而不是以用户的名义，向"服务提供商"
进行认证。严格地说，客户端模式并不属于OAuth框架所要解决的问题。在这种模式中，用户直接向客户端注册，客户端以自己的名义要求"
服务提供商"提供服务，其实不存在授权问题。

     +---------+                                  +---------------+
     |         |                                  |               |
     |         |>--(A)- Client Authentication --->| Authorization |
     | Client  |                                  |     Server    |
     |         |<--(B)---- Access Token ---------<|               |
     |         |                                  |               |
     +---------+                                  +---------------+

它的步骤如下：

> （A）客户端向认证服务器进行身份认证，并要求一个访问令牌。  
> （B）认证服务器确认无误后，向客户端提供访问令牌。

A步骤中，客户端发出的HTTP请求，包含以下参数：

granttype：表示授权类型，此处的值固定为"clientcredentials"，必选项。
scope：表示权限范围，可选项。

     POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=client_credentials

认证服务器必须以某种方式，验证客户端身份。

B步骤中，认证服务器向客户端发送访问令牌，下面是一个例子。

     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "example_parameter":"example_value"
     }

上面代码中，各个参数的含义参见《授权码模式》一节。

