
## 摘要 ##
node-gyp 更面向对象采用cpp 的class方法。
这篇[文章](https://juejin.cn/post/6844903971220357134)是一个很好的入门参考。

## 过程 ##
+ 准备好cpp 文件群
+ 写自己的node-addon-api 格式的wrapper
+ gyp configure
+ gyp build 得到*.node 二进制模块
+ 在JavaScript 程序中调用该对象函数


### 调试代码 ###

上述网址如果直接拷贝到vs2015 里编译会报错

```
严重性	代码	说明	项目	文件	行	禁止显示状态
错误	C2015	常量中的字符太多	greet	H:\exchange\f0\node16-gyp\src\index.cpp	9	

```
npm install 报错结果：
```

> gyp_call@1.2.3 install
> node-gyp rebuild

gyp info it worked if it ends with ok
gyp info using node-gyp@10.0.1
gyp info using node@16.14.2 | win32 | ia32
gyp info find Python using Python version 3.11.7 found at "C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe"

gyp info find VS using VS2015 (14.0) found at:
gyp info find VS "C:\Program Files (x86)\Microsoft Visual Studio 14.0"
gyp info find VS run with --verbose for detailed information
gyp info spawn C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe
gyp info spawn args [
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\gyp\\gyp_main.py',
gyp info spawn args 'binding.gyp',
gyp info spawn args '-f',
gyp info spawn args 'msvs',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build\\config.gypi',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\addon.gypi',
gyp info spawn args '-I',
gyp info spawn args 'C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2\\include\\node\\common.gypi',
gyp info spawn args '-Dlibrary=shared_library',
gyp info spawn args '-Dvisibility=default',
gyp info spawn args '-Dnode_root_dir=C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2',
gyp info spawn args '-Dnode_gyp_dir=H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp',
gyp info spawn args '-Dnode_lib_file=C:\\\\Users\\\\Administrator\\\\AppData\\\\Local\\\\node-gyp\\\\Cache\\\\16.14.2\\\\<(target_arch)\\\\node.lib',
gyp info spawn args '-Dmodule_root_dir=H:\\exchange\\f0\\node16-gyp',
gyp info spawn args '-Dnode_engine=v8',
gyp info spawn args '--depth=.',
gyp info spawn args '--no-parallel',
gyp info spawn args '--generator-output',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build',
gyp info spawn args '-Goutput_dir=.'
gyp info spawn args ]
node:internal/modules/cjs/loader:936
  throw err;
  ^

Error: Cannot find module 'node-addon-api'
Require stack:
- H:\exchange\f0\node16-gyp\[eval]
    at Function.Module._resolveFilename (node:internal/modules/cjs/loader:933:15)
    at Function.Module._load (node:internal/modules/cjs/loader:778:27)
    at Module.require (node:internal/modules/cjs/loader:1005:19)
    at require (node:internal/modules/cjs/helpers:102:18)
    at [eval]:1:1
    at Script.runInThisContext (node:vm:129:12)
    at Object.runInThisContext (node:vm:305:38)
    at node:internal/process/execution:76:19
    at [eval]-wrapper:6:22
    at evalScript (node:internal/process/execution:75:60) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [ 'H:\\exchange\\f0\\node16-gyp\\[eval]' ]
}
gyp: Call to 'node -p "require('node-addon-api').include"' returned exit status 1 while in binding.gyp. while trying to load binding.gyp
gyp ERR! configure error
gyp ERR! stack Error: `gyp` failed with exit code: 1
gyp ERR! stack at ChildProcess.<anonymous> (H:\exchange\f0\node16-gyp\node_modules\node-gyp\lib\configure.js:271:18)
gyp ERR! stack at ChildProcess.emit (node:events:526:28)
gyp ERR! stack at Process.ChildProcess._handle.onexit (node:internal/child_process:291:12)
gyp ERR! System Windows_NT 10.0.14393
gyp ERR! command "C:\\Program Files (x86)\\nodejs\\node.exe" "H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\bin\\node-gyp.js" "rebuild"gyp ERR! cwd H:\exchange\f0\node16-gyp
gyp ERR! node -v v16.14.2
gyp ERR! node-gyp -v v10.0.1
gyp ERR! not ok
npm ERR! code 1
npm ERR! path H:\exchange\f0\node16-gyp
npm ERR! command failed
npm ERR! command C:\Windows\system32\cmd.exe /d /s /c node-gyp rebuild

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\Administrator\AppData\Local\npm-cache\_logs\2024-03-12T09_30_25_649Z-debug-0.log
PS H:\exchange\f0\node16-gyp>
PS H:\exchange\f0\node16-gyp>
PS H:\exchange\f0\node16-gyp>
PS H:\exchange\f0\node16-gyp> npm install
Active code page: 65001
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'node-addon-api@8.0.0',
npm WARN EBADENGINE   required: { node: '^18 || ^20 || >= 21' },
npm WARN EBADENGINE   current: { node: 'v16.14.2', npm: '8.5.0' }
npm WARN EBADENGINE }

> gyp_call@1.2.3 install
> node-gyp rebuild

gyp info it worked if it ends with ok
gyp info using node-gyp@10.0.1
gyp info using node@16.14.2 | win32 | ia32
gyp info find Python using Python version 3.11.7 found at "C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe"

gyp info find VS using VS2015 (14.0) found at:
gyp info find VS "C:\Program Files (x86)\Microsoft Visual Studio 14.0"
gyp info find VS run with --verbose for detailed information
gyp info spawn C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe
gyp info spawn args [
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\gyp\\gyp_main.py',
gyp info spawn args 'binding.gyp',
gyp info spawn args '-f',
gyp info spawn args 'msvs',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build\\config.gypi',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\addon.gypi',
gyp info spawn args '-I',
gyp info spawn args 'C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2\\include\\node\\common.gypi',
gyp info spawn args '-Dlibrary=shared_library',
gyp info spawn args '-Dvisibility=default',
gyp info spawn args '-Dnode_root_dir=C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2',
gyp info spawn args '-Dnode_gyp_dir=H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp',
gyp info spawn args '-Dnode_lib_file=C:\\\\Users\\\\Administrator\\\\AppData\\\\Local\\\\node-gyp\\\\Cache\\\\16.14.2\\\\<(target_arch)\\\\node.lib',
gyp info spawn args '-Dmodule_root_dir=H:\\exchange\\f0\\node16-gyp',
gyp info spawn args '-Dnode_engine=v8',
gyp info spawn args '--depth=.',
gyp info spawn args '--no-parallel',
gyp info spawn args '--generator-output',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build',
gyp info spawn args '-Goutput_dir=.'
gyp info spawn args ]
gyp info spawn C:\Program Files (x86)\MSBuild\14.0\bin\MSBuild.exe
gyp info spawn args [
gyp info spawn args 'build\\binding.sln',
gyp info spawn args '/clp:Verbosity=minimal',
gyp info spawn args '/nologo',
gyp info spawn args '/p:Configuration=Release;Platform=Win32'
gyp info spawn args ]
Building the projects in this solution one at a time. To enable parallel build, please add the "/m" switch.
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  greeting.cpp
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  index.cpp
..\src\index.cpp(9): error C2015: ???????? [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
gyp ERR! build error
gyp ERR! stack Error: `C:\Program Files (x86)\MSBuild\14.0\bin\MSBuild.exe` failed with exit code: 1
gyp ERR! stack at ChildProcess.<anonymous> (H:\exchange\f0\node16-gyp\node_modules\node-gyp\lib\build.js:209:23)
gyp ERR! stack at ChildProcess.emit (node:events:526:28)
gyp ERR! stack at Process.ChildProcess._handle.onexit (node:internal/child_process:291:12)
gyp ERR! System Windows_NT 10.0.14393
gyp ERR! command "C:\\Program Files (x86)\\nodejs\\node.exe" "H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\bin\\node-gyp.js" "rebuild"\\bin\\node-gyp.js" "rebuild"gyp ERR! cwd H:\exchange\f0\node16-gyp
gyp ERR! node -v v16.14.2
gyp ERR! node-gyp -v v10.0.1
gyp ERR! not ok
npm ERR! code 1
npm ERR! path H:\exchange\f0\node16-gyp
npm ERR! command failed
npm ERR! command C:\Windows\system32\cmd.exe /d /s /c node-gyp rebuild

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\Administrator\AppData\Local\npm-cache\_logs\2024-03-12T09_30_59_367Z-debug-0.log

```
代码的双引号错写成单引号：
```
    std::string result = helloUser('Lorry');

```
改成 'std::string result = helloUser("Lorry");' 报错消失。

### 运行结果和日志 ###


```
PS H:\exchange\f0\node16-gyp> npm install
Active code page: 65001
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'node-addon-api@8.0.0',
npm WARN EBADENGINE   required: { node: '^18 || ^20 || >= 21' },
npm WARN EBADENGINE   current: { node: 'v16.14.2', npm: '8.5.0' }
npm WARN EBADENGINE }

> gyp_call@1.2.3 install
> node-gyp rebuild

gyp info it worked if it ends with ok
gyp info using node-gyp@10.0.1
gyp info using node@16.14.2 | win32 | ia32
gyp info find Python using Python version 3.11.7 found at "C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe"

gyp info find VS using VS2015 (14.0) found at:
gyp info find VS "C:\Program Files (x86)\Microsoft Visual Studio 14.0"
gyp info find VS run with --verbose for detailed information
gyp info spawn C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe
gyp info spawn args [
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\gyp\\gyp_main.py',
gyp info spawn args 'binding.gyp',
gyp info spawn args '-f',
gyp info spawn args 'msvs',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build\\config.gypi',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\addon.gypi',
gyp info spawn args '-I',
gyp info spawn args 'C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2\\include\\node\\common.gypi',
gyp info spawn args '-Dlibrary=shared_library',
gyp info spawn args '-Dvisibility=default',
gyp info spawn args '-Dnode_root_dir=C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2',
gyp info spawn args '-Dnode_gyp_dir=H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp',
gyp info spawn args '-Dnode_lib_file=C:\\\\Users\\\\Administrator\\\\AppData\\\\Local\\\\node-gyp\\\\Cache\\\\16.14.2\\\\<(target_arch)\\\\node.lib',
gyp info spawn args '-Dmodule_root_dir=H:\\exchange\\f0\\node16-gyp',
gyp info spawn args '-Dnode_engine=v8',
gyp info spawn args '--depth=.',
gyp info spawn args '--no-parallel',
gyp info spawn args '--generator-output',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build',
gyp info spawn args '-Goutput_dir=.'
gyp info spawn args ]
gyp info spawn C:\Program Files (x86)\MSBuild\14.0\bin\MSBuild.exe
gyp info spawn args [
gyp info spawn args 'build\\binding.sln',
gyp info spawn args '/clp:Verbosity=minimal',
gyp info spawn args '/nologo',
gyp info spawn args '/p:Configuration=Release;Platform=Win32'
gyp info spawn args ]
Building the projects in this solution one at a time. To enable parallel build, please add the "/m" switch.
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  greeting.cpp
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  index.cpp
..\src\index.cpp(9): error C2015: ???????? [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
gyp ERR! build error
gyp ERR! stack Error: `C:\Program Files (x86)\MSBuild\14.0\bin\MSBuild.exe` failed with exit code: 1
gyp ERR! stack at ChildProcess.<anonymous> (H:\exchange\f0\node16-gyp\node_modules\node-gyp\lib\build.js:209:23)
gyp ERR! stack at ChildProcess.emit (node:events:526:28)
gyp ERR! stack at Process.ChildProcess._handle.onexit (node:internal/child_process:291:12)
gyp ERR! System Windows_NT 10.0.14393
gyp ERR! command "C:\\Program Files (x86)\\nodejs\\node.exe" "H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\bin\\node-gyp.js" "rebuild"\\bin\\node-gyp.js" "rebuild"gyp ERR! cwd H:\exchange\f0\node16-gyp
gyp ERR! node -v v16.14.2
gyp ERR! node-gyp -v v10.0.1
gyp ERR! not ok
npm ERR! code 1
npm ERR! path H:\exchange\f0\node16-gyp
npm ERR! command failed
npm ERR! command C:\Windows\system32\cmd.exe /d /s /c node-gyp rebuild

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\Administrator\AppData\Local\npm-cache\_logs\2024-03-12T09_30_59_367Z-debug-0.log
PS H:\exchange\f0\node16-gyp> npm install
Active code page: 65001
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'node-addon-api@8.0.0',
npm WARN EBADENGINE   required: { node: '^18 || ^20 || >= 21' },
npm WARN EBADENGINE   current: { node: 'v16.14.2', npm: '8.5.0' }
npm WARN EBADENGINE }

> gyp_call@1.2.3 install
> node-gyp rebuild

gyp info it worked if it ends with ok
gyp info using node-gyp@10.0.1
gyp info using node@16.14.2 | win32 | ia32
gyp info find Python using Python version 3.11.7 found at "C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe"

gyp info find VS using VS2015 (14.0) found at:
gyp info find VS "C:\Program Files (x86)\Microsoft Visual Studio 14.0"
gyp info find VS run with --verbose for detailed information
gyp info spawn C:\Users\Administrator\AppData\Local\Programs\Python\Python311-32\python.exe
gyp info spawn args [
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\gyp\\gyp_main.py',
gyp info spawn args 'binding.gyp',
gyp info spawn args '-f',
gyp info spawn args 'msvs',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build\\config.gypi',
gyp info spawn args '-I',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp\\addon.gypi',
gyp info spawn args '-I',
gyp info spawn args 'C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2\\include\\node\\common.gypi',
gyp info spawn args '-Dlibrary=shared_library',
gyp info spawn args '-Dvisibility=default',
gyp info spawn args '-Dnode_root_dir=C:\\Users\\Administrator\\AppData\\Local\\node-gyp\\Cache\\16.14.2',
gyp info spawn args '-Dnode_gyp_dir=H:\\exchange\\f0\\node16-gyp\\node_modules\\node-gyp',
gyp info spawn args '-Dnode_lib_file=C:\\\\Users\\\\Administrator\\\\AppData\\\\Local\\\\node-gyp\\\\Cache\\\\16.14.2\\\\<(target_arch)\\\\node.lib',
gyp info spawn args '-Dmodule_root_dir=H:\\exchange\\f0\\node16-gyp',
gyp info spawn args '-Dnode_engine=v8',
gyp info spawn args '--depth=.',
gyp info spawn args '--no-parallel',
gyp info spawn args '--generator-output',
gyp info spawn args 'H:\\exchange\\f0\\node16-gyp\\build',
gyp info spawn args '-Goutput_dir=.'
gyp info spawn args ]
gyp info spawn C:\Program Files (x86)\MSBuild\14.0\bin\MSBuild.exe
gyp info spawn args [
gyp info spawn args 'build\\binding.sln',
gyp info spawn args '/clp:Verbosity=minimal',
gyp info spawn args '/nologo',
gyp info spawn args '/p:Configuration=Release;Platform=Win32'
gyp info spawn args ]
Building the projects in this solution one at a time. To enable parallel build, please add the "/m" switch.
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  greeting.cpp
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  index.cpp
cl : ??? warning D9002: ??????"/Zc:__cplusplus" [H:\exchange\f0\node16-gyp\build\greet.vcxproj]
  win_delay_load_hook.cc


  All 151 functions were compiled because no usable IPDB/IOBJ from previous compilation was found.

  greet.vcxproj -> H:\exchange\f0\node16-gyp\build\Release\\greet.node
  greet.vcxproj -> H:\exchange\f0\node16-gyp\build\Release\greet.pdb (Full PDB)
gyp info ok

up to date, audited 104 packages in 4s

15 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
PS H:\exchange\f0\node16-gyp> 

```

版本大相径庭：

```


PS H:\exchange\f0\node16-gyp> node --version
v16.14.2
PS H:\exchange\f0\node16-gyp> npm --version
Active code page: 65001
8.5.0
PS H:\exchange\f0\node16-gyp> npm install -g node-gyp
Active code page: 65001


```

## 下一步 ##
我不知道该用c接口还是用cpp 接口？
此处node=16.14
vs=2015.
都很cute。
那个稳定？哪个更符合现场情况。
根据情况实事求是。