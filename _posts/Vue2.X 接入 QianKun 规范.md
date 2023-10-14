## Vue2.X 接入 QianKun 规范


#### 主应用侧配置

> * 主应用侧配置中出现的文件路径请参考营销云中台项目目录


1. qiankun源资源配置文件 src/qkMicroApp.js

    > 注意：该配置文件分 线上prodAppConf 和 本地devAppConf

    ```javascript
	{
      title: "Minisite",										// 子应用title（必选，可随意命名）
      name: "issmart_minisite", 						// 子应用name（必选，且必须与子应用package.json中的name字段一致）
      path: "#/minisiteList",							  // 子应用激活路径（必选，且必须与子应用第一个路由路径一致） 
      container: '#subAppView',						// 子应用挂载点（必选，且必须存在挂载点）
      entry: "http://localhost:9999/"				// 子应用加载地址（必选，该地址分线上和线下环境）
    }
    ```
2. qiankun子应用激活白名单  src/views/marketing/activity/eventDetail/eventDetail.vue

	> 因为目前项目中的子应用加载方式为：iframe和qiankun，两种方式同时存在，所以设置了一个白名单。

    > 注意： 白名单配置在121行处，此白名单存储了将要使用qiankun接入的子应用的entery值,

    > 凡是加入到白名单中的子应用将会使用qiankun加载，否则将会使用iframe加载！

    ```javascript
    let qkAppEntry = ['forms', 'minisiteList/:id'];
    ```
3. qiankun子应用路由接入点配置 src/router/config.json

	> 这里一般全局只配置一次，后续接入如无特殊需求可忽略。具体配置规则见文档链接。

	```javascript
	{
      "path": "/marketing/activity/event/eventTool/micro/:microAppRoutes*",
      "component": "subAppContainer/subAppContainer",
      "name": "microapps",
      "meta": { "title": "eventTool", "icon": "eventManage", "isMicrApp": true }
    }
    ```


#### 微应用侧配置

> * 微应用侧配置中出现的文件路径请参考待接入的微应用项目目录

1. qiankun微应用入口文件 - main.js

    > 注意：入口文件需要导出qiankun的3个钩子函数：bootstrap、mount、unmount.否则微应用无法加载！

    > 3个钩子分别会自动在启动、挂载、卸载阶段触发

2. qiankun微应用路由文件配置 src/router/index.js

	> qiankun 接入尽量保持主应用、微应用的路由模式一致，推荐hash模式

    > qiankun微应用激活到加载过程，会从主应用传递过来一段prefix--前缀

    > prefix -- 本质就是当前微应用挂载于主应用的那个路由的path地址

    ```javascript
    {
      path: `${prefix}/forms`,
      name: 'FormList',
      component: FormList,
      meta: { name: '表单列表' }
    }
    ```
3. qiankun微应用webpack打包配置

    > 在vue.config.js中需要添加微应用打包时的配置（必须）

	```javascript
	output: {
      library: `${name}-[name]`,                  // 打包的库名
      libraryTarget: 'umd',                       // 打包格式使用umd
      jsonpFunction: `webpackJsonp_${name}`,      // 主应用以jsonp方式获取微应用静态资源时的函数名（标准解释见官方文档）
    }
	```






#### qiankun接入微应用可能遇到的问题

> 1. 本地开发接入qiankun时，子应用proxy代理无效，需要移到主应用proxy中，而且必须添加prefix！！

> 2. 线上部署接入qiankun时，主应用访问子应用存在两次跨域，静态资源跨域和接口请求跨域，需要运维协助处理！

> 3. 子应用打包后图片、字体资源的访问可能会404，需要使用file-loader、url-loader插件对publicpath进行设置！











#### 文档

[qiankun 官方文档 -- https://qiankun.umijs.org/zh/guide](https://qiankun.umijs.org/zh/guide)

[qiankun 官方github仓库 -- https://github.com/umijs/qiankun](https://github.com/umijs/qiankun)