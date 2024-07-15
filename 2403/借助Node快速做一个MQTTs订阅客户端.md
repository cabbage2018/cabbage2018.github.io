
## 背景 ##
作为IoT 项目的一个重要部分就是数据上云 数据上云我们往往借助轻量级多点发布工具例如MQTT/Mosquitto 。有时候App 上碰到最简单的故障就是设备未上数。本来很简单的一件事套了IoT 的笼头就说不清楚了：有可能设备掉线，有可能格式不对，有可能证书没配有可能密码不对有可能Kafka堵塞有可能Redis 内存crash 有可能数据库挂了有可能点位名称不匹配有可能网关序列号错误有可能json 缺少字段有可能topic研发改了有可能多了一个斜杠或者两个有可能digitaltwins 未作关联有可能用户无权限有可能队列满了 有可能时钟偏差到了UTC有可能编码不是utf-9。作为协议通信工作来说最好是二分法从中截断检查于是一个好用的MQTT 客户端往往就能够解决一大票问题。我以前用mqttbox 可惜并不稳定publish 端溢出的数据多了以后例如说每秒钟超过3条它就界面假死无法拷贝数据出来；也试过mqttx 反正也一般有一次搞一个mqtt 网址的带证书的代理服务器发现它使用前缀来区分mqtts和mqtt 对于某些别有用心故意反着来的服务器就会有大量沟通损耗。手边有node 但是以前的问题是无法分发。工作站电脑上不一定装了node 环境。前不久借助一个软件发布弄明白了pkg 的用法，于是顺手写了一个客户端，用来订阅代理并将 订阅消息保存本地文件。供事后分析。

## 代码 ##

```
const filepath_mqtt = path.join(process.cwd(), './mqtt.json');
if (fs.existsSync(filepath_mqtt)) {
    log.debug(`${filepath_mqtt} file found, updating mqtt-config`)
    options_mqtt = JSON.parse(fs.readFileSync(filepath_mqtt));
}
log.mark(options_mqtt.url);
let target = mqtt.connect(options_mqtt.url, options_mqtt.options);

target.on('error', function (err) {
    log.fatal('mqtt client connect #', err)
})
target.on('message', function (topic, message) {
    cyclic.trace(`${topic}, ${message}`)
})
let planStarter = "";
let permTask;
target.on('connect', function () {
    log.mark(`mqtt CONNECTED to logging...`)
    //sub

    target.subscribe('#')
    //unsub

    planStarter = setTimeout(() => {
        //pub
        target.publish('/datahub/remote/pub/reg', '#self-signed cert testing#;' + new Date().toISOString())
    }, 6000);

    permTask = setInterval(() => {
        target.publish('/datahub/remote/pub/reg', hostname + '#self-signed cert testing#;')
    }, 3 * 60000)
})

```
为了监测程序自己是不是还活着我多加了一个每若干分钟publish 的Interval 定时任务。
极简的要求的话还可以更少语句，不想再折腾能work 就行。

### 流程 ###
安装打包工具pkg
npm install -g pkg

### 配置 ###
package.json 
```
"scripts": {
    "pkg": "pkg . -t node16-win-x64 --out-path=dist/",
    "start": "node ./bin/www"
  },
  "pkg": {
    "assets": [
      "public/**/*",
      "views/**/*"
    ],
    "scripts": "routes/**/*.js"
  },
```
### 防火墙 ###
pkg 指令需要针对node版本和操作系统矩阵下载一个基础二进制运行环境会被卡脖子。

### 打包 ###
执行：
npm run pkg

结果：
Active code page: 65001

> mqtty@3.12.13 pkg
> pkg . -t node16-win-x64 --out-path=dist/

> pkg@5.8.1

执行完成将在dist/ 目录发现artifacts.
可执行文件exe，可以进行验证和分发了。
如果程序必须动态调用配置可以用path.join(process.cwd(), './myapp.log') 这种方式制定动态目标。我把mqtt 代理服务器的参数例如url 和用密放在同目录配置文件里。
这个简陋的工具吞吐量不简单能够一天接收上G 的消息。再也不会出现消息界面冻结无法拷贝的问题了。


