``` js
const client = new OSS({
      // yourRegion填写Bucket所在地域。以华东1（杭州）为例，yourRegion填写为oss-cn-hangzhou。
      region: 'yourRegion',
      // 从STS服务获取的临时访问密钥（AccessKey ID和AccessKey Secret）。
      accessKeyId: 'yourAccessKeyId',
      accessKeySecret: 'yourAccessKeySecret',
      // 从STS服务获取的安全令牌（SecurityToken）。
      stsToken: 'yourSecurityToken',
      // 填写Bucket名称，例如examplebucket。
      bucket: "examplebucket",
    });

    const options = {
      callback: {
        // 设置回调请求的服务器地址。
        url: 'http://examplebucket.aliyuncs.com:23450',
        // 设置回调请求消息头中Host的值，即您的服务器配置的Host值。
        // host: 'oss-cn-hangzhou.aliyuncs.com',
        // 设置发起回调时请求body的值。
        body: "bucket=${bucket}&object=${object}&etag=${etag}&size=${size}&mimeType=${mimeType}&imageInfo.height=${imageInfo.height}&imageInfo.width=${imageInfo.width}&imageInfo.format=${imageInfo.format}&my_var=${x:my_var}",
        // 设置发起回调请求的Content-Type。
        // contentType: 'application/x-www-form-urlencoded',
        // 设置发起回调请求的自定义参数。
        customValue: {
          var1: 'value1',
          var2: 'value2'
        }
      }
    }
    // 获取DOM。
    const submit = document.getElementById('submit')
    // 上传回调。
    submit.addEventListener('click', () => {
      client.put('test.txt', new Blob(['1234']), options).then(r => console.log(r))
    })

```

