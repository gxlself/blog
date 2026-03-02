---
title: gRpc-web在项目中的落地使用
tags: []
id: '236'
categories:
  - - gRPC
date: 2020-08-29 13:40:33
---

# gRPC概念

* * *

gRPC 是一个高性能、开源和通用的 RPC 框架，面向移动和 HTTP/2 设计。目前提供 C、Java 和 Go 语言版本，分别是：grpc, grpc-java, grpc-go. 其中 C 版本支持 C, C++, Node.js, Python, Ruby, Objective-C, PHP 和 C# 支持.

## 业务背景

后端项目繁多，算法部门要将旧有项目进行gRPC化，使用gRPC能够高效的在服务端间进行通信，所以，为了配合后端的gRPC更改，前端也使用gRPC-web，进行开发，与后端进行请求。 第一次接到这个需求，便开始了大量的gRPC的文档探索，以及将github的两个gRPC-web项目进行clone，然后深读代码，并进行尝试。项目使用react+antd+typescript全新搭建，按着文档进行依赖下载，也安装了go，本着自己完全跑一遍的心态试了一次，发现，这个项目过于久老，有个go的依赖一直出错。所以放弃了对这个本地调试，跑一遍demo的心态。 后端大哥本身是开发Go的，所以，他电脑装的比较齐全，提前踩好坑进行开发了，定义了一堆PB文件，然后开始发给我生成好的对应的ts以及js文件，这就让我不用再去纠结使用grpc github的仓库进行启动，并去自己操作一遍。   然后，开始开心的前端踩坑时刻，文档估计很久没更新了，使用的grpc-web库是0.4.0的版本，以下是我从官网拉过来的依赖文件。

```json
{
  "name": "grpc-web-commonjs-example",
  "dependencies": {
    "google-protobuf": "^3.6.1",
    "grpc-web": "^0.4.0"
  },
  "devDependencies": {
    "browserify": "^16.2.2",
    "webpack": "^4.16.5",
    "webpack-cli": "^3.1.0"
  }
}
```

前面也说了，这个文档大概很久没更新了，所以我安装后调用grpc的时候一直报错，百思不得其姐，只能在issue中查找对应的信息，这方面，国内项目看起来几乎没人使用，都是外国大哥们在使用，所以找的也是比较费力，总算是在茫茫issue中一眼看中了一个解答，那就是使用新的grpc-web库进行处理，这让茫然的我看到了一丝丝希望，果断将项目中的grpc-web库更新到了1.0.0

```json
{
  "dependencies": {
    "google-protobuf": "^3.6.1",
    "grpc-web": "^1.0.0"
  }
}
```

终于不负我所寄托，这次终于是接通了，让人欣喜。

## grpc-web的使用

使用方法主要是针对PB文件展开，后端生成的PB文件包含：

1.  定义方法，属性的PB文件
2.  定义请求client请求的PB文件

所以封装请求，主要是依靠将定义请求的PB文件进行导入，并按需处理

```
// 这是生成的client请求PB文件
import * as grpcWeb from 'grpc-web';
import * as common_pb from './common_pb';
import { UserLogin } from './bisys_pb';
// 请求实体
export class UserBehaviorClient { 
  constructor (hostname: string,
               credentials?: null  { [index: string]: string; },
               options?: null  { [index: string]: string; });

  login(
    request: UserLogin, // 请求参为UserLogin 
    metadata: grpcWeb.Metadata  undefined,
    callback: (err: grpcWeb.Error,
               response: RespLoginedMsg) =>; void
  ): grpcWeb.ClientReadableStream;
}


//?这是定义请求参的PB文件，请求参是 UserLogin 对象
import * as jspb from "google-protobuf"
import * as common_pb from './common_pb';

export class UserLogin extends jspb.Message {
  getAccount(): string;
  setAccount(value: string): UserLogin;

  getPassword(): string;
  setPassword(value: string): UserLogin;

  getToken(): string;
  setToken(value: string): UserLogin;

  serializeBinary(): Uint8Array;
  toObject(includeInstance?: boolean): UserLogin.AsObject;
  static toObject(includeInstance: boolean, msg: UserLogin): UserLogin.AsObject;
  static serializeBinaryToWriter(message: UserLogin, writer: jspb.BinaryWriter): void;
  static deserializeBinary(bytes: Uint8Array): UserLogin;
  static deserializeBinaryFromReader(message: UserLogin, reader: jspb.BinaryReader): UserLogin;
}
```

```
// 这是自己封装的service
import { baseUrl } from '@/config/base';
import { UserLogin } from '@/_proto/login/bisys_pb';
import { UserBehaviorPromiseClient } from '@/_proto/login/bisys_grpc_web_pb';

// 自己定义的interface
export interface LoginParamsType {
  userName: string;
  password: string;
  token: string;
}
export async function fakeAccountLogin(params: LoginParamsType) {
  // 首先引入对应的client实例化
  const userbehavior: UserBehaviorPromiseClient = new UserBehaviorPromiseClient(baseUrl);
  // 对请求参进行实例化
  const uselogin: UserLogin = new UserLogin();
  // 请求参的值赋值方式，写过java的 肯定很熟悉，这是对象的set操作
  // 看到这里，是不是想说取值也是get操作？是的！
  uselogin.setAccount(params.userName);
  uselogin.setPassword(params.password);
  uselogin.setToken(params.token);
  return userbehavior.login(uselogin);
}


//?使用该请求的地方?上面service已经对登录方法进行封装?所以在使用接口的地方直接引入
import?{ fakeAccountLogin }?from?'@/service/login';
import { RespLoginedMsg } from '@/_proto/login/bisys_pb';

const response: RespLoginedMsg = fakeAccountLogin();
// 到这里，基本上已经能够调通了，取值上面也说了，是get操作，但是grpc的数据流是二进制的，
// 所以虽然使用get操作能够取到对应的值，但是并不是我们常见的json格式，
// 这里就需要用到PB文件中定义的toObject()方法 这是你看到的就是平时请求返回的json格式
// 到这里，基本grpc你已经可以使用了 并且个人感觉很舒服，毕竟和TS搭配，
// 所以，这样写也避免了前端很多时候会传值出问题的情况，个人比较喜欢
console.log('response ===>', response.toObject())

```

## gRPC的部署

[部署这个grpc时的Nginx配置](https://github.com/grpc/grpc-web/blob/master/net/grpc/gateway/examples/echo/nginx.conf) envoy、grpcproxy代理
<!-- more -->
参考文献： [gRPC官方文档中文版](http://doc.oschina.net/grpc) [gRPC-web](https://grpc.io/docs/languages/web/basics/) [grpc-web github仓库](https://github.com/grpc/grpc-web) [使用grpc-web的demo github项目 参考](https://github.com/improbable-eng/grpc-web)