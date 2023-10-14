---
title: Json server && mockjs服务
categories: [Npm]
tags: [npm]
---

# Json-server & mockjs 搭建mock数据服务器



#### 项目目录

```txt
.
|-- json-server.json
|-- mockDB
|   |-- db.json
|   `-- testdb.js
|-- modules.js
|-- package.json
`-- server.js
```



#### 1. 依赖

```npm
npm i json-server mockjs
```

#### 2. 创建mock模块

```js
const Mock = require("mockjs");
const Random = Mock.Random;

const plan = () => {
  return Mock.mock({
    code: 200,
    succ: true,
    msg: 'ok',
    data: {
      'list|6-12': [
        {
          "id|+1": 1,
          "boyScore|1-100": 1,
          "girlScore|1-100": 1,
        }
      ],
    }
  });
}

module.exports = {
  plan,
}
```

#### 3. 组合mock模块

```js
 // 引入mockDB文件夹下的文件，假如有以下文件
 const testdata = require('./mockDB/testdb');
 
 // 这里定义一个收集的对象
const DB = {
  dbFunc: {},
  dbJson: {},
}
// 收集mock函数，在server.js会用到
DB.dbFunc = { ...testdata };

// 获取所有的api路径
const apiPath = Object.keys(DB.dbFunc);

// 组装json-server需要的路由表，这里为什么都是空对象呢？
// 上面说过，路由表的value必须是对象或者数组。但我们的mockDB文件夹下定义的都是mock函数，所以这里要保证路由表是正确的，就要重新组装一份符合格式的路由表。
apiPath.forEach(item => {
  DB.dbJson[item] = {};
});

module.exports = {
  ...DB
}
```

#### 4. 设置服务器

```js
const port = 9000;
const jsonServer = require('json-server');
// 创建json-server实例
const server = jsonServer.create();
const {dbFunc, dbJson} = require('./modules');
// 将路由配置表传入，生成路由表
const router = jsonServer.router(dbJson);
const middlewares = jsonServer.defaults();

// 这里比较重要。
// 目的是：重新定义路由返回的数据。就是说：每访问一个路由，返回response的时候，都可以经过router.render重新定义response数据
// 因为我们在db.js里将mock函数收集到了dbFunc对象里。
router.render = (req, res) => {
  // 根据请求的url，截取路径。
  // 例如：一般情况，res.url是/user_info?id=1234这样的格式。
  const end = req.url.indexOf('?');
  const apiName = end === -1 ? req.url.slice(1) : req.url.slice(1, end + 1);
  // 根据apiName拿到对应的mock函数，然后返回mock函数生产的mock数据
  // 因为每次返回response的时候，都会执行一次该函数，所以得到的都是新的mock数据
  const response = dbFunc[apiName] && dbFunc[apiName]();
  res.send(response || {});
};

server.use(middlewares);
// 导入路由
server.use(router);
// 在port端口开服务
server.listen(port, () => {
  console.log(`JSON Server is running at http://localhost:${port}`);
});
```



**引用**

[json-server + mock.js搭建前后端分离的mock服务](https://juejin.cn/post/6844903912772730887)

