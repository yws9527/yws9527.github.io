## 阿里 DataV 地图v3接口线上403排错

- DataV.GeoAtlas地理小工具系列

  https://datav.aliyun.com/portal/school/atlas/area_selector?spm=5176.smartservice_service_robot_chat_new.0.0.3fe3709add9qjL

- 问题现象

  > 在localhost环境，地图v3接口能正常访问和调试，一旦使用IP进行访问或者部署到线上，v3接口就会报403（阿里客服反馈也无解）

- 解决思路

  1. 怀疑线上需要付费；

     *已排除*

  2. 怀疑v3接口有防盗链机制，简而言之就是防御了【**referer**】

- 解决过程

  - 开发环境

    ```ts
    proxy: {
          ws: true,
          target: target,
          changeOrigin: true,
          rewrite: (path) => path.replace(new RegExp(`^${prefix}`), ''),
          // https is require secure=false
          ...(isHttps ? { secure: false } : {}),
          // 绕过防盗链，固定referer，让v3接口认为我们一直在localhost环境使用
          headers: {
            Referer: 'http://localhost',
          },
        };
    ```

    

  - 生产环境

    ```nginx
    location ^~ /v3/ {
      proxy_pass        https://geo.datav.aliyun.com/areas_v3/;
      
      # 设置nginx反向代理时的referer[固定写法]，绕过防盗链，让v3接口认为我们一直在localhost环境使用
      proxy_set_header  referer    "http://localhost";
      
      proxy_set_header  X-Real-IP        $remote_addr;
      proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
      proxy_set_header  X-NginX-Proxy    true;
    }
    ```