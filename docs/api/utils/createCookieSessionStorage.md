---
title: createCookieSessionStorage
---

# createCookieSessionStorage

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.createCookieSessionStorage.html)

创建并返回一个 SessionStorage 对象，将所有会话数据直接存储在会话 Cookie 本身中。

这样做的好处是不需要数据库或其他后端服务，并且可以简化某些负载均衡场景。但它也有一个限制，即序列化的会话数据不能超过浏览器的最大 Cookie 大小。需要权衡取舍！
