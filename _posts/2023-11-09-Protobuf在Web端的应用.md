---
title: Protobuf在Web端的使用应用
categories: [Protobuf]
tags: [protobuf]
---



## 1. Protobuf 简介

Google Protocol Buffer( 简称 Protobuf) 是 Google 公司内部的混合语言数据标准，目前已经正在使用的有超过 48,162 种报文格式定义和超过 12,183 个 .proto 文件。他们用于 RPC 系统和持续数据存储系统。

Protocol Buffers 是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，或者说序列化。它很适合做数据存储或 RPC 数据交换格式。可用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化结构数据格式。目前提供了 C++、Java、Python 三种语言的 API（即时通讯网注：Protobuf官方工程主页上显示的已支持的开发语言多达10种，分别有：C++、Java、Python、Objective-C、C#、JavaNano、JavaScript、Ruby、Go、PHP，基本上主流的语言都已支持，详见工程主页 [protobuf][Github仓库]）。



## 2. protobuf在Web端的使用

1. 目录

   .
   |...
   |-- protobuf				# protobuf 目录
   |   |-- Class.proto
   |   |-- Grade.proto
   |   |-- MessageType.proto
   |   |-- School.proto
   |-- server				   # 服务端目录
   |   |-- db.ts
   |   |-- error.ts
   |   |-- index.ts
   |   |-- real.ts
   |   |-- utils.ts
   |-- src
   |... 
   |   |-- protoTs			# protobuf生成ts目录
   |   |   |-- Class.ts
   |   |   |-- Grade.ts
   |   |   |-- MessageType.ts
   |   |   |-- School.ts

   |...
   `-- vite.config.ts

2. 准备基础

   > 1. 需要预先了解下Protobuf3的基础知识
   > 2. 推荐npm全局安装esno，好处是可以直接解析运行ts而不借助其他库

3. Web端使用vue3、TS、vite

   1. 依赖安装

      ```sh
      # 安装
      npm install protobufjs protoc @protobuf-ts/runtime
      ## protobufjs 核心包
      ## protoc proto文件的编译器
      ## @protobuf-ts/runtime ts运行时工具
      
      # 众所周知，在vite构建工具下，无法使用commonjs模块，而protobufjs库最新版是无法安装的
      npm install vite-plugin-commonjs @protobuf-ts/plugin -D
      ## vite-plugin-commonjs 将 CommonJS 模块转换为 ES6 模块
      ## @protobuf-ts/plugin proto编译器插件
      
      # 在vite.config.ts中添加如下配置，使用默认配置即可
      import commonjs from 'vite-plugin-commonjs';
      ...
      plugins: [
        ...,
        commonjs(),
      ],
      ```

   2. script配置

      - [protobuf-ts配置文档][[protobuf-ts配置文档]

      ```sh
      "scripts": {
      		...
          "remove": "rm -rf src/protoTs/*",
          "generate": "npx protoc --ts_out src/protoTs/ --ts_opt long_type_string --ts_opt generate_dependencies --proto_path protobuf protobuf/*.proto",
          "protobuf": "npm run remove && npm run generate",
          "server": "esno server/index.ts"
        }
        
        # 将已经定义的protobuf生成ts，具体配置请查阅[protobuf-ts配置文档]
        pnpm run generate
        
        # 清理旧的ts并生成新的ts
        pnpm run protobuf
      
      ```

   3. 定义protobuf文件

      ```txt
      syntax = "proto3";
      package framework;
      
      // 消息请求体
      message PBMessageRequest {
        uint32 type = 1;                            // 消息类型
        optional bytes messageData = 2;             // 请求数据
        uint64 timestamp = 3;                       // 客户端时间戳
        string version = 4;                         // api版本号
        string token = 5;                           // 用户登录后服务器返回的 token，用于登录校验
      }
      
      // 消息响应体
      message PBMessageResponse {
        uint32 type = 1;                            // 消息类型
        optional bytes messageData = 2;             // 返回数据
        optional uint32 resultCode = 3;             // 返回的结果码
        optional string resultInfo = 4;             // 返回的结果消息提示文本（用于错误提示）
      }
      
      // 所有的接口
      enum PBMessageType {
        getSchoolList = 0;                         // 获取学校列表, PBSchoolListReq => PBSchoolListRsp
        getSchoolTreeList = 1;                     // 获取学校树形列表, {} => PBSchoolListRsp
      }
      ```

   4. 请求接口过程

      ```ts
      import { PBMessageRequest, PBMessageResponse, PBMessageType } from '@/protoTs/MessageType';
      
      
      // 请求数据
      const reqData = {
        token: token,
        type: _msgType,
        version: apiVersion,
        timeStamp: Date.now(),
        messageData: requestBody,
      };
      // 将对象序列化成请求体实例
      const req = PBMessageRequest.create(reqData);
      
      // 将请求数据encode成二进制，encode是proto.js提供的方法
      function transformRequest(data: PBMessageRequest) {
        return PBMessageRequest.toBinary(data);
      }
      ```

      

   5. 处理响应过程

      ```ts
      import { PBMessageRequest, PBMessageResponse, PBMessageType } from '@/protoTs/MessageType';
      
      function isArrayBuffer (obj: any) {
        return Object.prototype.toString.call(obj) === '[object ArrayBuffer]';
      }
      
      function transformResponseFactory(responseType: any) {
        return function transformResponse(rawResponse: number | number[] | null | undefined) {
          // 判断response是否是arrayBuffer
          if (rawResponse == null || !isArrayBuffer(rawResponse)) {
            return rawResponse;
          }
          try {
            // 请求时已设定responseType为'arraybuffer'，所以这里需要预先处理数据
            const enc = new TextDecoder('utf-8');
            const raw = JSON.parse(enc.decode(new Uint8Array(rawResponse as number[])));
            const buf = protobuf.util.newBuffer(Object.values(raw));
            // const buf = protobuf.util.newBuffer(rawResponse);
            // decode响应体
            const decodedResponse = PBMessageResponse.fromBinary(buf);
            if (decodedResponse.messageData && responseType) {
              // const model = protoRoot.lookup(responseType);
              // decodedResponse.messageData = model.decode(decodedResponse.messageData);
              decodedResponse.messageData = responseType.fromBinary(decodedResponse.messageData);
            }
            return decodedResponse;
          } catch (err) {
            throw err;
          }
        }
      }
      ```

   6. 完整请求封装代码

      ```ts
      
      import protobuf from 'protobufjs'
      import { storage } from './storage';
      import httpService, { apiVersion } from '@/config/services';
      import { PBMessageRequest, PBMessageResponse, PBMessageType } from '@/protoTs/MessageType';
      
      type ImesgType = keyof typeof PBMessageType;
      
      function getMessageTypeValue(msgType: ImesgType) {
        return PBMessageType[msgType];
      }
      
      /**
       * 
       * @param {*} path 接口地址
       * @param {*} msgType 接口名称
       * @param {*} responseType 返回体类型
       * @param {*} requestBody 请求体参数
       */
      function request(path: string, msgType: ImesgType, responseType: any, requestBody?: Uint8Array, ) {
        // 得到api的枚举值
        const token = storage.get('token');
        const _msgType = getMessageTypeValue(msgType);
      
        // 请求需要的数据
        const reqData = {
          token: token,
          type: _msgType,
          version: apiVersion,
          timeStamp: Date.now(),
          messageData: requestBody,
        };
        // 将对象序列化成请求体实例
        const req = PBMessageRequest.create(reqData);
        const transformResponse = transformResponseFactory(responseType);
        httpService.defaults.headers['satoken'] = token;
        
        // 调用axios发起请求
        // 这里用到axios的配置项：transformRequest和transformResponse
        // transformRequest 发起请求时，调用transformRequest方法，目的是将req转换成二进制
        // transformResponse 对返回的数据进行处理，目的是将二进制转换成真正的json数据
        return httpService
          .post(path, req, { transformRequest, transformResponse })
          .then(({ data, status }) => {
            if (status !== 200) throw new Error('服务器异常');
            return data;
          },(err) => {
            throw err;
          })
      }
      
      // 将请求数据encode成二进制
      function transformRequest(data: PBMessageRequest) {
        return PBMessageRequest.toBinary(data);
      }
      
      function isArrayBuffer (obj: any) {
        return Object.prototype.toString.call(obj) === '[object ArrayBuffer]';
      }
      
      function transformResponseFactory(responseType: any) {
        return function transformResponse(rawResponse: number | number[] | null | undefined) {
          // 判断response是否是arrayBuffer
          if (rawResponse == null || !isArrayBuffer(rawResponse)) {
            return rawResponse;
          }
          try {
            // 请求时已设定responseType为'arraybuffer'，所以这里需要预先处理数据
            const enc = new TextDecoder('utf-8');
            const raw = JSON.parse(enc.decode(new Uint8Array(rawResponse as number[])));
            const buf = protobuf.util.newBuffer(Object.values(raw));
            // decode响应体
            const decodedResponse = PBMessageResponse.fromBinary(buf);
            if (decodedResponse.messageData && responseType) {
              decodedResponse.messageData = responseType.fromBinary(decodedResponse.messageData);
            }
            return decodedResponse;
          } catch (err) {
            throw err;
          }
        }
      }
      
      // 在request下添加一个方法，方便用于处理请求参数
      request.create = function (pbConstruct: any, data: any) {
        const _data = pbConstruct.create(data);
        return pbConstruct.toBinary(_data);
      }
      
      export default request;
      ```

      

## 3. protobuf在服务端的使用，以express为例

```ts
import { PBMessageResponse } from '@/protoTs/MessageType';

function genResponse(school: PBSchoolListRsp | undefined) {
  const data = {
    type: 2,
    resultCode: 200,
    messageData: PBSchoolListRsp.toBinary(school!),
  };
  return PBMessageResponse.toBinary(data);
}

app.post('/schoolTree', express.text({ type: '*/*' }), async function(req, res) {
  // 请求头的信息
  console.log('headers', req.headers);
  // 因为请求体是buff的文本，需要先转化为buff
  const buffer = Buffer.from(req.body);
  // 从req.body解码得到请求体
  const _reqData = PBMessageRequest.fromBinary(buffer);
  console.log('reqdata', _reqData);
  globalMap.clear();
  try {
    if (req.headers.satoken) {
      globalMap.set('satoken', req.headers.satoken);
      service.defaults.headers['satoken'] = req.headers.satoken;
      await genData(2, generTreeSchool);
      const _resData = genResponse(treeSchools);
      // console.log('respdata', _resData);
      res.send(_resData);
    } else {
      errorHandle(res, { succ: false, code: 902, msg: '未登录' });
    }
  } catch (error: any) {
    errorHandle(res, error);
  }
});

export function errorHandle(res: any, error: any) {
  const resdata = PBMessageResponse.toBinary({
    type: 1,
    resultInfo: error?.msg,
    resultCode: Number(error?.code),
  });

  res.send(resdata);
}
```



## 4. protobuf使用总结

- 请求时

  按照对应业务定义的proto规则，对请求数据编码，再按照请求封装的数据结构对整个请求体统一编码，最后发送数据

- 响应时

  先将文本类型的buffer转化为数据buffer，然后按照响应封装的数据结构对数据buffer解码，再按照对应的业务定义的proto规则，对已经初步解码的数据再次解码，最后获取真实数据



## 5. protobuf3 学习参考地址

- [Protobuf3 语言指南][Protobuf3 语言指南]

- [protobuf3 官方文档翻译][protobuf3 官方文档翻译]

- [protobuf 学习指南(proto3)][protobuf 学习指南(proto3)]

- [Protobuf通信协议详解：代码演示、详细原理介绍等][Protobuf通信协议详解：代码演示、详细原理介绍等]

  



[Github仓库]: https://github.com/protocolbuffers/protobuf
[protobuf API文档]:https://protobufjs.github.io/protobuf.js/index.html
[Protobuf3 语言指南]: https://github.com/lixiangyun/protobuf_doc_ZH_CN
[Protobuf通信协议详解：代码演示、详细原理介绍等]: https://zhuanlan.zhihu.com/p/141415216
[protobuf 学习指南(proto3)]: https://juejin.cn/post/6978474549025177608
[protobuf3 官方文档翻译]:https://zhuanlan.zhihu.com/p/400088101
[protobuf-ts配置文档]: https://github.com/timostamm/protobuf-ts/blob/main/MANUAL.md
[demo练习]:https://github.com/yws9527/protobuf-demo

