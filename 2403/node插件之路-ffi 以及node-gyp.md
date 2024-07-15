我们为了解决问题，往往不得不先引入一个更大更普遍的问题。
是所谓工程。

## 摘要 ##
ffi 以及 node-gyp 仿佛都能实现addon。最大的区别是node 支持的版本差异。node8 对ffi 支持较好，C 的接口，利用ref 框架转换数据结构；node-gyp 我可以使用16.14 以及18.x 进行编译，得到myaddon.node 二进制。后者因为需要下载预编译的二进制平台软件包所以叫做prebuild 的addon 方案。 支持ffi 的node 版本上限我没测，不会大于16. 因为node16 无法引用ffi/ 它引用成功的是ffi-napi。

## 过程 ##
+ 首先用vs 编译工具生成一个dll 动态链接库
+ 安装ffi 以利用上述二进制软件
+ 从node8 里利用JavaScript 代码引用并运行dll 里的接口函数
+ 多次调用
+ 清理资源和内存吧


### 配置 ###
项目 package.json
```
{
    "name": "ffi_call",
    "version": "1.2.3",
    "engines": {
        "node": ">=8.0.0 <12.22.0"
    },
    "description": "An example application call .dll",
    "main": "program.js",
    "dependencies": {
        "bindings": "~1.5.0",
        "ffi": "~2.3.0",
        "ref": "~1.3.5",
        "ref-array": "1.2.0",
        "ref-struct": "1.1.0",
        "prebuild-install": "^7.1.1"
    },
    "scripts": {
        "start": "node program.js"
    },
    "author": "fungi0",
    "license": "GPL",
    "devDependencies": {
        "prettier": "2.6.0"
    }
}

```

### 代码和 ###
写一个program.js 注册接口并 调用dll
```
'use strict'

var ref = require("ref");
var ffi = require("ffi");

//第一个形参为dll所在位置（dll文件可以不用加.dll），第二个为函数信息
console.log("__dirname:", __dirname)
var libm = ffi.Library(__dirname + '/bin/Win32Dll1.dll', {
    //函数名
    'fun': ['int', ['string', 'string']]
});

//调用
var str1 = "acc";
var str2 = "bionology,,,,";
console.log("Result from dll:", libm.fun(str1, str2));
```


## 下一步 ##
研究开源库60870 的wrapper C代码。
以便封装60870 库的dll。