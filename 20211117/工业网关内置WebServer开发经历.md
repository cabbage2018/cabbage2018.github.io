## 从一个工业网关内置Web Server出发 ##
不会别的，只会写bug:) 
我打算利用这样一个工程来学习Javascript / Node.js 的Web 前后端编程。实际上我打算不提这个名词的，因为我想将网站设计成一个整体，但是在引入Jsonwebtoken 这个技术时遇到了困难。研究了一番后发现，前后端原来不是人为能够决定的排除特定的命名它们是必然的存在，只不过工程师选择了这么样的术语来称呼。后端指服务器这个比较好理解，实际上也是服务器生成了所有的前端网页 - 不管你乐意不乐意，网页（姑且不提前端这个概念）就像编程时用到的回调函数一样，不仅包含了静态文本和图片，最重要的是掺入了验证令牌和脚本。这样看来网页是后端生成的，怎么能和它平起平坐呢？ 说实话也就是一个区分而已可能不必介意。这个结构又让我想到了一个工业网关的设计- 它引入一个配置文件，这个文件里的设备树形结构的根节点竟然是自己，彻底晕掉了，配置文件的自己不能修改自己的IP地址而是必须进入到硬件的自己的配置里面去修改自己的IP地址。。。 我的脑子已经不好使了算了算了不描述这么反人类的设计。所以在我没接触到JWT 和SSO 这样的技术概念的时候我反感这种前后端的设计，觉得有一天我自己来做就把这两个桥是桥路是路整整清楚。目前看来这个理想做不到了。无论如何简单的网页即便是静态html 这样的如果要在请求头里植入服务器分发的令牌（token）也需要动用脚本把函数调用链条推进到服务器端的程序。从这个意义上说我明白了什么是真正的静态网页什么是动态网页。静态网页还真不仅内容是静态的，它里面也不能掺杂JWT cookie信息返回这样的逻辑！这样下发给浏览器就不再去验证它和响应它，服务端不再用函数去响应该网页。当然现今互联网世界里严格来说没有这样的东西，只有我们自己手写的demo项目或者表计内置的网站可能出现这类东西。这类网站的特点是不会连接到外网，倒是可以，将某个时刻的设备状态和数据切片呈现为一个静态网页。与此相对，如果甲方说这样不好必须让数据实时展现，OK，于是你在网页里加个定时器每秒去刷新数据；或者利用ajax 回调动态展示后台数据。这些都牵涉到服务端代码的执行。因为这实际上耦合了硬件IO这条线无法避免前端后台的函数调用。
对了后台不仅仅指数据库，也包括硬件的数据，例如来自IO 或者协议的数据。除此之外没了。

### 网站的核心功能 ###
这是开发之前每人都必须要问清楚的。通常来说工业网关的目标是展示数据。这个描述非常简单，但是甲方和一些宣称自己不懂技术的人往往往这个描述上加上不要钱的定语修饰，例如
- 快速的
- 高速更新
- 差不多实时的
- 近实时的
- 实时的

这是性能维度； 同时，因为开发这类内部使用的产品不要钱（要钱就是良好的商业而免费的东西我总觉得助长的就是暴力愚蠢这也就是如来为什么让孙悟空他们取西经的本意佛只渡有缘人）脑洞往往可以大开，于是一次次的产品需求和设计会议上就会有以下内容慢慢的满满的慢慢的渐渐的进来：
+ 支持10个设备
+ 支持15个设备
+ 支持20个设备
+ 支持25个设备
+ 支持30个设备
+ 支持50个设备
+ 支持100个设备
+ 支持10000个设备

只要产品没做出来这个数量还可以往上增加。因为他在描述这个设备点位时会说我不要求时效性，意思是每秒钟采样一个设备你们慢慢采不就得了，时效性在部署时却又会回到实时要求上来-来来来，你看这个领导来看，这个跳闸信号过了30秒才上来这个说不过去吧~ 况且，他不是很懂吗！所以谦虚的人最可怕，他是项目的杀手，国内的客户甲方不少擅长这类的前后矛盾便于最后把项目搞砸拒绝验收。所以在国内这需求分析师真的必须要有权力和地位。
实际上呢这些指标也不是做不到，但是没有商业价值。什么叫做得到？ 让开发工程师每次配20台次设备，那么即使MODBUS 技术也能在100ms 以下采集到这些响应；过一会再轮换20 台设备，就这么做肯定没问题。但是没有商业价值。甲方往往几千块钱CR都不愿承认怎能指望为了实现这个梦幻功能配上一名专职设备连接工程师？ 产品首先要固定，项目要验收，最讨厌对任何问题都回答没问题技术上能做到。什么叫技术上什么叫现实上。
这里设备的概念也有很多讲究或者叫猫腻，可能刚开始需求方定义30个点位周期采数，随着项目的开展一个能源管理EMS 项目觉得电度能耗量不够而慢慢的需要温度、录波、谐波失真、无功功率，这些属于电能质量分析、调试、资产健康、工作状态变量都要采了。

### 怎么实现 ###
成功的产品故事都是由生产方强力定义明确的产品来生产和演进。一个产品型号20年畅销全球，绝对是设计的功力。这里面以前可是没有软件什么事的（:(）机械，材料，结构，外观这些唱了主角。成功的背后并没有语言和架构以及脚手架什么事。我于是随便选择了一个Node以及Express，因为国内的搜索引擎只能搜到这些。再套上一个易懂的前端模板叫做EJS。
网站现今不难做了，任何一款高端点的硬件设备例如电能质量型电表、低压断路器、保护装置、测控装置，PLC 等都内置了Webserver。虽然是静态的，但是能显示数。如果实现网关势必可能暴露在公网，于是乎security 超过功能成了最重要和最基本的能力。Webserver security 不是我等工程师能搞的，肉食者谋之，我们能做的是遵循讨论让网站能够安全运行下去并被别人搜索以及看到而不是黑掉。寻寻觅觅找到了几项也许必须要遵循或者实现的功能，英文说比较准确authentication, authorization, role management, SSO, 除此之外的细枝末节概念仅为支撑技术。这是任何一款Internet Web站点的基础。如果你听说过SaaS，PaaS，Cloud，IoT platform概念，那么深入接触就会明白这些概念又建立在上述4个重要功能点上。例如工业物联云操作系统就是采用租户这种方法，类似电商里的多商户插件的作用，将平台资源按需分配给租户并收取使用费和租金。
- authentication: 身份鉴别
- authorization: 授权核准
- role management: 角色资源管理，实际上应该称职位资源管理，职位权限分配和管理
- SSO: 单点登录，类似你用jd购物以及GfP 游戏时你选择用wechat 授权（大部分机会仅仅授权而不用登录）

身份鉴别分成signup，login，forgot password这些能力；授权核准包括jwt token的实现，即sign 令牌，分发令牌，verify 令牌，以此核准用户上网机器设备身份，并可以在payload 荷载里携带不敏感信息例如用户Id 这类黑客拿到也不知道含义的信息，但对于服务器来说至少可以定位到用户的信息；职位权限分配和管理例如RBAC 基于role的访问控制，这个功能提供类似租户体量的资源访问控制，例如small，middle，large 用户分别能否生成多少云上资产设备实例数目可以在RESTful API 访问时检查该用户的购买size来限制。能够拿来卖钱的属性这里都做成了可以配置的软件控制变量。当你发现云服务商说技术原因只能升级不能降级你就会稍稍明白了。单点登录针对的是cluster集群 部署的多个微服务，实在烦了每个APP 必须让用户输入用户名和密码于是产生了这个概念，利用一台登录服务器记录用户的状态，减少在不同微服务间切换时都必须登录的次数。

### 尝试Express + neDB 的 authentication ###
因为neDB 支持一键安装于是我首选了该NoSQL 做这个原型。这里的任务很明确，实现用户注册signup， 注册完了login登录，定期forget密码找回密码。这样一个最小化身份鉴别系统不需要数据，你可以仅仅展示一些静态网页来证明用户确实可以看到。将来可以将用户和他/她建立的设备数据之间关联和实现数据透明。这几个阶段与数据库的交换大概是
- 注册成功时写用户身份到数据库
- 登录时根据password 和salt 组合hash coded来确保知道口令和用户名的人才能进入网站
- 忘记密码找回功能需要第三方背书例如邮件或者手机短信，通过给用户的邮箱或者手机短信发送一个6位代码方便用户重设密码。更高级也许是所谓的电商免密购物？

signup 路由里get和post页面可以这么写
```
...

router.get('/signup/', async function (req, res, next) {
  res.render('signup', {
    title: 'Sign me up' + new Date().toISOString(),
  })
})
...
router.post('/signup/', async function (req, res, next) {
  const { username, password } = req.body
  db.User.findOne({ where: { email: username }})
  .then((usr)=>{
    if(usr){
      return next(new Error('Email has been already registered'))
    }else{
      let salt = crypto.randomBytes(16).toString('hex')
	  
      // Hashing user's salt and password with 1000 iterations, 64 length and sha512 digest
      let hashcode = crypto.pbkdf2Sync(password, salt, 1000, 64, `sha512`).toString(`hex`)
      let User1 = new db.User({
        email: username,
        password: hashcode,
        role: 'nonexist',
        salt: salt,
        createdAt: new Date(),
        updatedAt: new Date()
      })
      // Save new User object to database
      db.User.create(User1)
      .then(confirmation=>{
        return res.status(201).send({
          message : "User added successfully.",
          detail: confirmation
        })
      })
      .catch(err=>{
        return res.status(400).send({
          message : "Failed to add user." + err
        })
      })
    }
  })
  .catch((err)=>{
    return next(new Error('Server backend error' + err))
  })
})
...

```
首先我们不会费力不讨好的存储所谓的用户password 代替方法是加盐后存储这个信息的摘要，所以即使我们服务器端代码让黑客都猜到了也不能帮助他拿到特定用户的密码，当然，如果窥探者刻意要知道某个特定用户的密码则刻意通过其他系统外方法例如肉鸡和电磁辐射泄漏等物理手段获取到，与网站的安全性关系不大。我们总不能假设服务器和浏览器交互的所有信息都透明吧，明明世界上那么多大厂投入精英人才和大把钞票来做这块标准。说实话跟我从头到尾实现这几个功能就能对浏览器工程师和HTTP 通信有更切肤的感受。总而言之，当今网络世界安全基于这样的常识：泄露了这段服务器代码无助偷窥者偷窃密码。本来能拿到的依然能拿到，本来拿不到的依然拿不到。后台存数据库可以放到任何关系型或者文本对象数据库，前者像postgreSQL 后者类似nedb，没有特定的门槛。

如果幸运的话注册成功后你大概率能在数据库里找到该用户的信息，我们默认她/他用邮件地址注册。因为目前我不想购买短信服务以便能够支持手机号即时无注册登录:(。

login 功能涉及令牌生成我们放到下节。

### 前后端合力的基于jwt token思想的 authorization ###
从前我写工业云的Node.js APP时傻傻分不清authorization 和authentication，以为Bearer 后面跟的都是密码。（人类实际上不能用机器写出人类的文学作品，哪怕一小段也不行，不信去看128bit 的uuid 都难得重复你耗尽宇宙的能量能把128位长的作品都打印出来吗？针对以前看到一个科普作品发出的感慨，大部分人都是似是而非不懂装懂而且不愿意承认自己的无知包括我自己，在真正探寻宇宙前我们还是重新发现一下自己的内心世界）如果把两者厘清还是很容易理解的。就是，如果
- 把鉴权成功结果形成一个令牌，并存储到浏览器cookie
- 每次用户访问资源不用重新鉴权而是看浏览器是不是携带令牌，如果是，并且利用令牌的signature 签名的解密算法和服务器的全局（！）对称密钥（也可以非对称，没时间细研究）解密该令牌成功那么就推断和信任该用户登录是成功的
- 我不用session ，因为据说需要服务器存储该信息；我也不用纯cookie 信息，或者Base64"加密"的所谓安全方法，或者网络上一大把的各式各样的奇奇怪怪的没有弄明白原理就自己尝试实现shabby和罪恶之源的私有版jwt 思想。我相信经过广大外国大小厂码农验证过并广泛被攻击了并且成功扛住了大规模攻击的工具有它存在的意思- 这就是还是上面那条，你即使非常懂jwt 这样的令牌技术也无助你破解用户密码窃取用户信息也不能降低这种风险，该不安全的还是不安全，该安全的依然安全。
- 我花了一天时间左右研究怎么实现request header 里携带"authorization" 这类jwt 令牌字段，隐隐约约感觉到大概有两种方法，下文细讲。这个部分对我来说在此次demo project 里最最难，最终还是读各种开源module 包的源代码才找到些些线索，再一次再一次告诉我自己除了有价值的源码不要去读代码了。20年前源代码仿佛宝贝一样被大家藏着掖着而现如今这东西像臭狗屎一样有识之士唯恐避之不及因为你闻多了臭味写出来的也会臭了
- 借助这一步，也就是调用"jsonwebtoken": "^7.4.1" 和 "express-jwt": "^5.3.3" 2 模块的过程，彻底陷入了前后端悖论，你中有我我中有你逻辑错乱了。并且在诸如"Access-Control-Allow-Credentials: true" 各种设置中慢慢理解了实际上在前端后端之间还横亘了一个浏览器browser，戴脚镣的程序员就是要在各种browser火炭上跳舞因为据说为了安全后者阉割掉了一些原本很easy life的请求头和cookie 的读取存储使用。真正要取得Web安全的话这位工程师必须理解前后端各种特性以及限制，话说，理解了也不一定能做好，例如google停止Chrome浏览器对第三方Cookie的支持。还好只是第三方cookie

login 时生成令牌
```
...
router.get('/login', function(req, res, next) {
  res.render('login', {
    title: 'Login Form Tutorial@' + new Date().toISOString(),
  })
})
...
// token payload includes userId(email)
router.post('/login', function (req, res, next) { 
  const { username, password } = req.body
  db.User.findOne( { where: { email: username }})
    .then(found => {
      const hashcode = crypto.pbkdf2Sync(password, found.salt, 1000, 64, `sha512`).toString(`hex`)
      if (found.password === hashcode && (hashcode.indexOf(found.password) >=0 )) {
        const now = Math.floor(Date.now() / 1000)
        let payload = {
          sub: found.id,
          iat: (now),
          nbf: (now - 10)};

        jsonwebtoken.sign(payload, _secret, {algorithm: 'HS256'}, function(err, token) {
          if(err){
            err.more = 'Error occurred while generating token'
            return next(err)
          } else {
            if(token){
              res.header('authorization', `Bearer ${token}`);
              res.header('Set-Cookie', `authorization=Bearer ${token}; path=/; HttpOnly`);
              return res.status(200).json({message : "User Logged In"})
            }else{
              return next(new Error('Could not generate token'));
            } 
          }
        })
      } else {
        return res.status(400).send({message : "Inaccurate Password"})
      }
    })
    .catch(err => {
      return res.status(404).send('User not found')
    })
})
...
```
这段代码jsonwebtoken.sign() 函数调用即异步响应生成令牌的过程。可以看到荷载里可以携带不敏感信息例如用户Id，这个在下面访问其它页面核验身份时需要，本站其它url核验令牌的代码段

```
app.use(jwt({
  secret: _secret,
  getToken: (req)=>{
    let myCookie = req.headers.cookie.replace(/(?:(?:^|.*;\s*)authorization\s*\=\s*([^;]*).*$)|^.*$/, "$1");
    // 显示: World
    let parts = myCookie.split(' ');
    if (parts.length == 2) {
      let scheme = parts[0];
      let content = parts[1];
      if (/^Bearer$/i.test(scheme)) {
        return content
      }
    }
    return next(new Error('No token could be found in Cookie'))
  }
}).unless({
  path: ['/users/login', '/users/signup', '/']  // 指定路径不经过 Token 解析 
}))

```
以上引用了express-jwt 中间件（所谓中间件实际上指符合req, res, next 函数签名的一种Webserver 函数，起到全局拦截http request并核验的作用，完成自己的工作后通过Javascript 特有的字面量函数调用 return next() 将处理权转让给下一个中间件，Express 有价值的模块都是中间件，在程序里按排列顺序执行）这段代码是我根据Auth0 的express-jwt 源码生成的-关键部分为注入了一个getToken 回调函数，这个函数起到从浏览器发送来的req 的cookie里剥离Bearer token的作用，而，这个Bearer token 是在上一个函数login里由服务器发送给浏览器的，拿出来看是这句

```
              res.header('Set-Cookie', `authorization=Bearer ${token}; path=/; HttpOnly`);
```

这也就是我研究了一大天后得到的结果： 服务器必须有某种方法分发token 给浏览器及用户网页，cookie 是一种浏览器维护的挂在特定域名下的私有数据存储，应该放在浏览器的数据库里，因为关闭浏览器重新打开往往还能load 到旧的cookie只要还在生命期内。这里我规定了1h=3600s 的有效期，结果发现到期后还真能失效！可以说整个编程里最结棍的地方就是分发token或者说如何让浏览器允许网页携带特定的token（还是由cookie携带，还是别村localstorage 而是让浏览器自己管理，否则安全准则改变后这个程序就不灵了）。天知道我怎么了解到服务器可以通过'Set-Cookie' 这个响应头来请求browser 自动在接下来的请求header里携带这个特定的信息，因为express-jwt 原生的check 是检查preflight预检CORS 请求里面和客户端req header里authorization 字段有没有携带令牌，这条路径我理解无需客户端网页代码来嵌入该header字段（即令牌）。还有一种我想到的是借助客户端网页Javascript 脚本来查询cookie并贴到请求头里。后者总让人不放心，因为提高了信息泄露的可能性，算了我研究了半天还是放弃了。但是我在mindsphere 的登录页面里找到的js 代码里发现它就是借助后者实现的，因为从调试界面可以看到服务器和浏览器交换的信息都是cookie以及Set-Cookie 这种，而未出现authorization header。通过研究这，一次合格的服务器和浏览器交互包括客户端请求（通常F5 或者输入网址或者点击登录按钮等）以及服务器响应组成，因此F12 调试网页能看到的都是一对信息。但是但是这一切都是在服务器的远程遥控下的动作，像牵线木偶或者放风筝一样。客户端一切的数据和操作理应都是服务器fabricated 的结果，用户只有选择的自由，不能干扰服务器的逻辑和流程。

通过国际版bing 搜索到的有用的信息，[Sending and Receiving Cookies from Express.js](https://alligator.io/nodejs/express-cookies/#:~:text=Getting%20Cookies%20in%20Express%20A%20great%20utility%20is,highly%20recommend%20you%20check%20out%20the%20source%20code.)

程序员的悲哀： 你是正常人可能无法理解一个程序员在调试jwt 看到这个消息的内心兴奋"jwt expired" - which means我们的令牌真的生效而且检查了它的生命周期。