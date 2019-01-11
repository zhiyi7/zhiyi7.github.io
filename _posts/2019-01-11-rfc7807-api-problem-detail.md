---
layout: post
title: JSON API 错误详情的指导标准 RFC7807
---

HTTP 规定了一些状态码来表示请求错误，但针对错误具体信息没有规定怎么显示，一般只有一句话 Page Not Found 或者 Internal Server Error。现代 JSON API 通常需要返回具体错误信息来方便前端进行控制，实践中大家也都在规定自己的错误信息格式。

IETF 有一个还未最终成型的 [RFC7807](https://tools.ietf.org/html/rfc7807) 试图解决这个问题。

它引入了一个 `application/problem+json` 的 media type，用普通错误码当做返回码，把错误信息按下面例子放进 body 里：

```
HTTP/1.1 403 Forbidden
Content-Type: application/problem+json
Content-Language: en

{
"type": "https://example.com/probs/out-of-credit",
"title": "You do not have enough credit.",
"detail": "Your current balance is 30, but that costs 50.",
"instance": "/account/12345/msgs/abc",
"balance": 30,
"accounts": ["/account/12345",
                "/account/67890"]
}
```
也可以扩展，比如：
```
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json
Content-Language: en

{
"type": "https://example.net/validation-error",
"title": "Your request parameters didn't validate.",
"invalid-params": [ {
                        "name": "age",
                        "reason": "must be a positive integer"
                    },
                    {
                        "name": "color",
                        "reason": "must be 'green', 'red' or 'blue'"}
                    ]
}
```

标准挺简单的，一共就几个固定字段，可以看下原文。遵循同一个标准的错误处理，好处是可以用别人造好的轮子。php 就有一个遵循 RFC7807 叫 [ApiProblem](https://github.com/Crell/ApiProblem) 的轮子。