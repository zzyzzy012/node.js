# Node.js

## 简介

- express包搭建web服务器。
- electron构建跨平台的桌面应用。
- restify搭建API接口。

node.js运行js文件。

```
node 文件名.js
```

cmd中快捷指令

⬆快速定位到上一次执行的命令。

tab键快速补全文件名，需要先敲几个字母匹配。

esc清空当前输入的命令。

cls清空终端所有内容。

## 1.操作文件

### 1.1.readFile()

```js
const fs = require('fs');

fs.readFile('国色芳华.txt', 'utf-8', (err, data) => {
  if (err) {
    // 读取成功为 null
    console.log(`文件读取失败，原因是：${err}`);
    
  }
  console.log(`文件读取成功：${data}`);
})
```

### 1.2 writeFile()

```js
const content_txt2 = '夫妻二人各怀心思穿过国公府一重又一重的院子，总算是到了祠堂。蒋重淡淡地看了焕然一新，面色也不怎么好看地站在祠堂外头等他的蒋长扬一眼，朝和他打招呼的几个族老点点头，随即昂首挺胸走入祠堂中。'
// 覆盖原先内容
fs.writeFile('国色芳华.txt', content_txt2, {flag: 'a+'}, (err) => {
  if (err) console.log(`文件写入失败，原因是：${err}`);
  console.log(`文件写入成功`);
})
```

| 标志 | 描述                                         | 如果文件不存在，则会创建它 |
| ---- | -------------------------------------------- | -------------------------- |
| `r+` | 此标志打开文件进行读写                       | ❌                          |
| `w+` | 此标志打开文件进行读写，并将流定位在文件开头 | ✅                          |
| `a`  | 此标志打开文件进行写入，并将流定位在文件末尾 | ✅                          |
| `a+` | 此标志打开文件进行读写，并将流定位在文件末尾 | ✅                          |

### 1.3 文件路径

使用`./`等相对路径容易出现node.js路径自动拼接错误的问题。使用绝对路径`__dirname + '/文件路径/文件名'`。

```js
const fs = require('fs');

fs.readFile(__dirname +'/国色芳华.txt', 'utf-8', (err, data) => {
  if (err) {
    // 读取成功为 null
    console.log(`文件读取失败，原因是：${err}`);
    
  }
  console.log(`文件读取成功：${data}`);
})
```

不推荐用`+`号拼接，不安全，推荐用`path.join(__dirname, '/文件路径/文件名')`。`../`会抵消一级路径。

```js
const fs = require('fs');
const path = require('path');

const filePath = path.join(__dirname, '/国色芳华.txt')
fs.readFile(filePath, 'utf-8', (err, data) => {
  if (err) {
    // 读取成功为 null
    console.log(`文件读取失败，原因是：${err}`);
    
  }
  console.log(`文件读取成功：${data}`);
})
```

`path.basename()`获取路径中的文件名。可接收2个参数，第二个参数表示文件扩展名。

```js
const path = require('path');

const filePath = path.join(__dirname, '/国色芳华.txt')
const baseFile = path.basename(filePath)
console.log(baseFile) // 国色芳华.txt

const baseFile = path.basename(filePath, '.txt')
console.log(baseFile) // 国色芳华
```

`path.extname()`获取文件路径中的拓展名部分。

```js
const path = require('path');

const filePath = path.join(__dirname, '/国色芳华.txt')
const extName = path.extname(filePath)
console.log(extName)  // .txt
```

### 1.4 文件开关

`fs.open()`和`fs.close()`。

| 标志 | 描述                                         | 如果文件不存在，则会创建它 |
| ---- | -------------------------------------------- | -------------------------- |
| r+   | 此标志打开文件进行读写                       | ❌                          |
| w+   | 此标志打开文件进行读写，并将流定位在文件开头 | ✅                          |
| a    | 此标志打开文件进行写入，并将流定位在文件末尾 | ✅                          |
| a+   | 此标志打开文件进行读写，并将流定位在文件末尾 | ✅                          |

```js
const fs = require('fs');
const path = require('path');

const filePath = path.join(__dirname, '/国色芳华.txt')
fs.open(filePath, 'r+', (err, fd) => {
  if (err) {
    console.log(`文件打开失败，原因是：${err}`);
  }
  console.log(`文件打开成功，文件描述符为：${fd}`);
})
```

## 2.http

127.0.0.1这个IP对应的域名时localhost。`http://localhost:80 | http://127.0.0.1:80`。

端口号（Port Number）是计算机网络中用于标识特定网络服务或应用程序的编号。它与IP地址一起使用，帮助设备准确地找到并与指定的服务或程序进行通信。`HTTP: 80 | HTTPS: 443 | FTP: 21 | SSH: 22`。

`res.setHeader('Content-Type', 'text/html;charset=utf-8')`解决中文乱码问题。

`res.end(content) `将内容返给客户端。

```js
const http = require('http');
const fs = require('fs');
const path = require('path');

let fPath = ''

const server = http.createServer()
server.on('request', (req, res) => {
  let url = req.url
  let content = '404 Not Found'
  if (url === '/' || url === '/index.html') {
    fPath = path.join(__dirname, '../01操作文件/国色芳华.txt')
    fs.readFile(fPath, (err, data) => {
      if (err) res.end(content)
      content = data
      res.setHeader('Content-Type', 'text/html;charset=utf-8')
      res.end(content) 
    })
  } else if (url === '/about.html') {
    content = '<h1>About Us</h1>'
    res.setHeader('Content-Type', 'text/html;charset=utf-8')
    res.end(content) 
  }
})

server.listen(80, () => {
  console.log('Server is running at http://localhost:80')
})
```

## 3.module

分文内置模块(`fs | path | http ...`) 、自定义模块(以文件路径的方式引入，可以省略js拓展名，node.js会自动补全)、第三方模块(需要下载包)。

第三方包和内置包重名优先内置包。

补全优先级：`.js | .json | .node`。

模块有块作用域，其他文件要访问模块内容只能访问通过`module.exports`(可以简写为`exports`)导出去的部分。

`require`导入的模块以`module.exports`导出的**对象**为准，当`exports`和`module.exports`导出的**对象**发生冲突时，优先`module.exports`。

```js
exports.username = '张三
module.exports.gender = '男'
// 导出内容是 {username: '张三', gender: '男'}
```

method.js

```js
const singer = "Taylor Swift"
const sing = () => {
  console.log(`${singer} is g great woman singer!`)
}
module.exports = {
  singer,
  sing,
}
```

01.module.js

```js
const customeM = require('./method')
console.log(customeM.singer)
```

## 4.npm

```powershell
npm install 包名称
npm i 包名称
// @版本号
npm i dayjs@1.11.13
// 一次性安装所有依赖包。package.json中dependencies记录的包
npm i
// 卸载包，无法缩写
npm uninstall 包名称
```

`npm init -y`快速初始化（自动模式），生成package.json文件。

`-y`参数表示自动接受所有默认值，无需逐步回答问题。

```json
{
  "name": "your-project-name",
  "version": "1.0.0",
  "description": "",
  "main": "index.js", // 入口文件
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

`npm init`手动模式。

项目名称（`name`）

版本号（`version`）

描述（`description`）

（`entry point`，如 `index.js`）

测试命令（`test command`）

Git 仓库（`git repository`）

关键字（`keywords`）

作者（`author`）

许可证（`license`）

------

如果某些包只在项目开发阶段会用到，在项目上线之后不会用到，建议把包安装在`devDependencies`节点中。

```powershell
npm i 包名 --save-dev  // 全称
npm i -D 包名  // npm i 包名 -D 也可以，前者更常用，更直观
```

如果某些包在开发和项目上线之后都需要用到，建议把包安装在`dependencies`节点中。

切换下包服务地址

```powershell
// 查看下包镜像源
npm config get registry
// 切换下包镜像源为淘宝镜像源
npm config set registry https://registry.npmjs.org
// 检查镜像源是否下载成功
npm config get registry
```

nrm切换下包的镜像源

```powershell
npm i nrm -g
// 查看所有可用的镜像源
nrm ls
  npm ---------- https://registry.npmjs.org/
  yarn --------- https://registry.yarnpkg.com/
  tencent ------ https://mirrors.tencent.com/npm/
  cnpm --------- https://r.cnpmjs.org/
  taobao ------- https://registry.npmmirror.com/
  npmMirror ---- https://skimdb.npmjs.com/registry/
  huawei ------- https://repo.huaweicloud.com/repository/npm/
// 将下包的镜像源切为淘宝镜像
nrm use taobao
```

全局包，包下载为止在`C:\Users\用户名称\AppData\Roaming\npm\node_modules`。

`C:\Users\ziyan\AppData\Roaming\npm\node_modules`。

```powershell
npm install --global 包名称
npm i -g 包名称
npm uninstall -g 包名称
```

nodemon自动监测代码变化，帮助重启项目。

```powershell
node 文件名
nodemon 文件名
```

## 5.express

`app.send()`将处理好的内容发送给客户端，可识别json数据和普通文本数据。

`req.query`可以访问客户端通过查询字符串的形式，发送到服务器的参数。`/user/?id=123/?name=John`。

`req.params`可以访问到url中通过`:`匹配到的动态参数。`/user/:id/:name`。`/user/123/tom`。

```js
const express = require('express');

const app = express()
const port = 3000
app.get('/user', (req, res) => {
  console.log(req.query)
  res.send('Hello World!')
})
app.post('/user/:id/:name', (req, res) => {
  console.log(req.params)
  res.send('Got a POST request')
})
app.put('/user', (req, res) => {
  res.send('Got a PUT request')
})
app.listen(3000, () => {
  console.log('Server is running on port 3000')
})
```

`express.static()`访问静态资源。

```js
const express = require('express');

const app = express()
const port = 80
app.use(express.static('./static'))
// app.use('/static', express.static('./static'))
app.listen(80, () => {
  console.log('Server is running at http://localhost:80')
})
```

路由的模块化

```js
const express = require('express');
const router = express.Router();

router.get('/user', (req, res) => {
  res.send('user page');
})
router.post('/login', (req, res) => {
  res.send('login page');
})

module.exports = router
```

```js
const express = require('express');
const router = require('./router')

const app = express()
const port = 80
app.use(express.static('./static'))
// app.use('/static', express.static('./static'))
app.use(router)
app.listen(80, () => {
  console.log('Server is running at http://localhost:80')
})
```

### 5.1 中间件

中间件(middle ware)，可以共享`req,res`，将这些内容传递给路由。多个中间件之间，共享同一份 req 和 res。可以在上游的中间件中，统一为 req 或 res 对象添加自定义的属性或方法，供下游的中间件或路由进行使用。

要在路由之前注册中间件。在`next()`函数之后不要再写代码。

```js
const mw = (req, res, next) => {
  console.log('mv middleware')
  req.startTime = dayjs(Date.now()).format('YYYY-MM-DD hh:mm:ss')
  next()
}
```

`app.use(mw)`创建全局中间件。

```js
const mw = (req, res, next) => {
  console.log('这是最简单的中间件')
  req.startTime = new Date()
  next()
}

app.use(mw)

app.get('/', (req, res) => {
  res.send(`home page, ${req.startTime}`)
})

app.get('/user', (req, res) => {
  res.send(`user page, ${req.startTime}`)
})
```

创建多个局部中间件。

```js
// 局部生效的中间件
router.get('/login', mw1, mw2..., (req, res) => {
  res.send(`login page, start time: ${req.startTime}`)
})
router.get('/login', [mw1, mw2...], (req, res) => {
  res.send(`login page, start time: ${req.startTime}`)
})
```

```js
const express = require('express')
const dayjs = require('dayjs')

const app = express()
const router = express.Router()

const port = 80

function mw (req, res, next) {
  console.log('mv middleware')
  req.startTime = dayjs(Date.now()).format('YYYY-MM-DD hh:mm:ss')
  next()
}
// 局部生效的中间件
router.get('/login', mw, (req, res) => {
  res.send(`login page, start time: ${req.startTime}`)
})
router.get('/user', mw, (req, res) => {
  res.send(`user page, start time: ${req.startTime}`)
})
router.get('/home', mw, (req, res) => {
  res.send(`home page, start time: ${req.startTime}`)
})
router.get('/logout', mw, (req, res) => {
  res.send(`logout page, start time: ${req.startTime}`)
})
app.use(router)
// 全局生效的中间件
app.use(mw)

app.listen(80, () => {
  console.log(`http://localhost:${port}`)
})
```

中间件分类

1. 应用级别中间件：**通过`app.use() | app.get() | app.post()`，绑定到app实例上的中间件**，叫做应用级别中间件。
2. 路由级别中间件：**绑定到`express.Router()`实例上的中间件，叫做路由级别的中间件。**它的用法和应用级别中间件没有任何区别。
3. 错误级别中间件：用来捕获整个项目中发生的异常错误，从而防止项目异常崩溃的问题。函数包含四个参数：`(err, req, res, next)`，只能在路由之后注册使用。
4. express内置中间件：
   1. `express.static()`快速托管静态资源的内置中间件，例如: HTML 文件、图片、CSS 样式等。
   2. `express.json()`解析 JSON 格式的请求体数据。
   3. `express.urlencoded`解析 URL-encoded 格式的请求体数据。
5. 第三方中间件

```js
const express = require('express')

const app = express()
const router = express.Router()
const port = 80

// 应用级中间件
mva = (req, res, next) => {
  console.log('这是应用级中间件')
  next()
}
app.use(mva)

// 路由级中间件
const mw = (req, res, next) => {
  console.log('这是路由级中间件')
  next()
}
router.use('/user', mw, (req, res) => {
  res.send('user page')
})
app.use(router)

// 错误处理中间件，只能在路由之后使用
app.get('/home', (req, res) => {
  throw new Error('这是错误')
  res.send('home page')
})
app.use((err, req, res, next) => {
  console.log('这是错误处理中间件')
  res.send(`err: ${err.message}`)
})

// 内置中间件
// express.static()：提供静态文件服务
app.use(express.static('./static'))

// express.json()：解析 application/json 请求体
app.use(express.json())
app.get('/json', (req, res) => {
  console.log(req.body)
  res.send(`${req.body}, 这是 JSON 数据`)
})

// express.urlencoded()：解析 application/x-www-form-urlencoded 请求体
app.use(express.urlencoded({ extended: false }))
app.get('/url', (req, res) => {
  console.log(req.body)
  res.send(`${req.body}, 这是 urlencoded 数据`)
})

// express.text()：解析 text/plain 请求体
app.use(express.text())
app.get('/text', (req, res) => {
  console.log(req.body)
  res.send(`${req.body}, 这是 text 数据`)
})

// express.raw()：解析 application/octet-stream 请求体
app.use(express.raw())
app.get('/raw', (req, res) => {
  console.log(req.body)
  res.send(`${req.body}, 这是 raw 数据`)
})

app.listen(80, () => {
  console.log(`http://localhost:${port}`)
})
```

自定义中间件

手动模拟一个类似于`express.urlencoded`这样的中间件，来解析 POST 提交到服务器的表单数据。

```js
const express = require('express')
const qs = require('querystring')

const app = express()
const port = 80

app.use((req, res, next) => {
  // 在中间件中，需要监听 req 对象的 data 事件，来获取客户端发送到服务器的数据。
  // 如果数据量比较大，无法一次性发送完毕，则客户端会把数据切割后，分批发送到服务器。
  // data 事件可能会触发多次，每一次触发 data 事件时，获取到数据只是完整数据的一部分，需要手动对接收到的数据进行拼接。
  let str = ''
  req.on('data', (chunk) => {
    str += chunk
  })
  req.on('end', () => {
    // console.log(str)
    req.body = qs.parse(str)
    next()
  })
})

app.get('/user', (req, res) => {
  res.send(req.body)
})

app.listen(80, (req, res) => {
  console.log(`Server is running at http://localhost:${port}`)
})
```

### 5.2 接口

```js
const express = require('express');
// 跨域问题
const cors = require('cors');

const app = express();
// 需要在路由注册前使用
app.use(cors());
const router = express.Router();
// 解析请求参数
express.urlencoded({ extended: false });

// get请求
router.get('/api/get', (req, res) => {
  const data = req.query
    res.send({
      status: 200,
      msg: 'GET 请求成功',
      data,
    });
})
// post请求
router.post('/api/post', (req, res) => {
  const data = req.body;
  res.send({
    status: 200,
    msg: 'POST 请求成功',
    data,
    });
})

module.exports = router;
```



```js
const express = require('express');
const router = express.Router();

router.get('/api/get', (req, res) => {
  const data = req.query
    res.send({
      status: 200,
      msg: 'GET 请求成功',
      data,
    });
})

module.exports = router;
```

### 5.3 跨域

**cors相关的三个响应头**

```js
Acess-Control-Allow-Origin: <origin> | *
// origin参数的值指定了允许访问该资源的外域url，* 表示允许来自任何域的请求

res.setHeader('Acess-Control-Allow-Origin', 'http:xxx.com')
```

`Access-Control-Allow-Headers`默认情况下，CORS 仅支持客户端向服务器发送如下的 9个请求头:

`Accept、Accept-Language、Content-Language、DPR、Downlink,Save-Data、Viewport-Width、 Width、Content-Type`(值仅限于`text/plain、multipart/form-data、application/x-www-form-urlencoded`三者之一)。

如果客户端向服务器发送了额外的请求头信息，则需要在服务器端，通过` Access-Control-Allow-Headers`对额外的请求头进行声明，否则这次请求会失败。

```js
//允许客户端额外向服务器发送 Content-Type 请求头和 X-Custom-Header 请求头
// 注意:多个请求头之间使用英文的逗号进行分割
res.setHeader('Access-Control-Allow-Headers', 'content-Type, X-Custom-Header')
```

`Access-Control-Allow-Methods`默认情况下，CORS 仅支持客户端发起`GET、POST、HEAD`请求。如果客户端希望通过`PUT、DELETE`等方式请求服务器的资源，则需要在服务器端，通过`Access-Control-Alow-Methods`来指明实际请求所允许使用的`HTTP`方法。

```js
// 只允许 POST、GET、DELETE、HEAD 请求方法
res.setHeader('Access-Control-Allow-Methods', 'POST, GET, DELETE, HEAD')
// 允许所有的 HTTP 请求方法
res.setHeader('Access-Control-Allow-Methods'.·*')
```

------

**简单请求与预检请求**

简单请求：

1. 请求方式:`GET、POST、HEAD`三者之一。
2. `HTTP`头部信息不超过以下几种字段:`无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width、Content-Type(只有三个值application/x-www-form-urlencoded、multipart/form-data、text/plain)`。

预检请求：在浏览器与服务器正式通信之前，浏览器会先发送`OPTION`请求进行预检，以获知服务器是否允许该实际请求，所以这一次的`OPTION`请求称为“预检请求”。服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据。

1. 请求方式为`GET、POST、HEAD`之外的请求`Method`类型
2. 请求头中包含**自定义头部字段**
3. 向服务器发送了`application/json`格式的数据

------

**JSONP请求**:浏览器端通过`<script>`标签的`src`属性，请求服务器上的数据，同时，服务器返回一个函数的调用。这种请求数据的方式叫做`JSONP`。

1. JSONP 不属于真正的 Ajax 请求，因为它没有使用 XMLHttpRequest 这个对象
2. JSONP 仅支持`GET`请求，不支持`POST、PUT、DELETE`等请求。

如果项目中已经配置了 CORS 跨域资源共享，为了防止冲突，必须在配置 CORS 中间件之前声明 JSONP 的接口。否则JSONP 接口会被处理成开启了CORS 的接口。

```js
//优先创建 JSONP 接口【这个接口不会被处理成 CORS 接口】
(req,res)=>{})2 app.get('/api/jsonp
// 再配置 CORS 中间件【后续的所有接口，都会被处理成 CORS 接口】
app.use(cors())
// 这是一个开启了 CORS 的接口
app.get('/api/get',(req,res)=>{ })
```

实现 JSONP 接口的步骤：

1. 获取客户端发送过来的回调函数的名字
2. 得到要通过 JSONP 形式发送给客户端的数据
3. 根据前两步得到的数据，拼接出一个函数调用的字符串
4. 把上一步拼接得到的字符串，响应给客户端的<script>标签进行解析执行

```js
app.get('/api/jsonp',(req,res)=>{
  // 1.获取客户端发送过来的回调函数的名字
  const funcName = reg.query.callback
  // 2.得到要通过 JSONP 形式发送给客户端的数据
  const data= { name:'zs',age:22 }
  // 3.根据前两步得到的数据，拼接出一个函数调用的字符串
  const scriptStr= `${funcName}(${JSON.stringify(data)})`
  // 4.把上一步拼接得到的字符串，响应给客户端的<script>标签进行解析执行
  res.send(scriptstr)
})
```

## 6.mysql

安装mysql

```
npm i mysql
```

导入mysql模块，连接mysql数据库。

```js
const mysql = require('mysql')

const db = mysql.createPool({
  host: '127.0.0.1',
  port: 3306, 
  user: 'root',
  password: '123456',
  database: 'my_dtbase_01'
})

// 检测 mysql 模块是否能正常工作
db.query('select * from users', (err, result) => {
  if (err) console.log(err.message)
    console.log(result)
})
```

数据库没链接成功，报错解决方式：

```
ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456'
```

插入数据，`?`占位。

```js
// 插入数据
const user = {username: 'dustin', password: 'dt5656' }
const sqlstr = 'insert into users (username, password) values (?, ?)'
db.query(sqlstr, [user.username, user.password], (err, result) => {
  if (err) console.log(err.message)
  if (result.affectedRows === 1) {
    console.log('插入数据成功')
  }
})

// 插入数据便捷版
const user = {username: 'arica', password: 'aa8888' }
const sqlstr = 'insert into users set ?'
db.query(sqlstr, user, (err, result) => {
  if (err) console.log(err.message)
  if (result.affectedRows === 1) {
    console.log('插入数据成功')
  }
})
```

更新数据

```js
// 更新数据
const user = {id: 7, username: 'gigi', password: 'gg0000' }
const sqlstr = 'update users set username = ?, password = ? where id = ?'
db.query(sqlstr, [user.username, user.password, user.id], (err, result) => {
  if (err) console.log(err.message)
  if (result.affectedRows === 1) {
    console.log('更新数据成功')
  }
})

// 更新数据便捷版
const user = {id: 7, username: 'riri', password: 'rr0000' }
const sqlstr = 'update users set ? where id = ?'
db.query(sqlstr, [user, user.id], (err, result) => {
  if (err) console.log(err.message)
  if (result.affectedRows === 1) {
    console.log('更新数据成功')
  }
})
```

删除数据，如果只有一个`?`占位符，可以省略直接写。

标记删除，不是真正删除，而是更新status字段，标记是否删除

```js
// 删除数据
const id = 12
const sqlstr = 'delete from users where id = ?'
db.query(sqlstr, id, (err, result) => {
  if (err) console.log(err.message)
  if (result.affectedRows === 1) {
    console.log('删除数据成功')
  }
})

// 标记删除
const id = 11
const sqlstr = 'update users set status = ? where id = ?'
db.query(sqlstr, [1, id], (err, result) => {
  if (err) console.log(err.message)
  if (result.affectedRows === 1) {
    console.log('标记删除成功')
  }  
})
```

## 7.身份认证

对于服务端渲染和前后端分离这两种开发模式来说，分别有着不同的身份认证方案。

- 服务端渲染推荐使用 Session 认证机制
- 前后端分离推荐使用 JWT 认证机制

express-session，身份认证。

可以通过`req.session`来访问和使用session对象，从而存储用户的关键信息。

```js
const express = require('express');
const session = require('express-session');

const app = express();

app.use(session({
    secret: 'keyboard cat',
    resave: false,
    saveUninitialized: true
}))

app.post('/api/login', (req, res) => {
  if (req.body.username !== 'admin' && req.body.password !== '123456') {
    return res.send({ status: 1, msg: '登陆失败' })
  }
  req.session.user = req.body
  req.session.islogin = true
  res.send({ status: 0, msg: '登陆成功' })
})

app.post('/api/user', (req, res) => {
  if (!req.session.islogin) {
    res.send({ status: 1, msg: '请先登录' })
  }
  res.send({
    status: 0,
    msg: '获取用户信息成功',
    data: req.session.user.username
  })
})
```

Session 认证机制需要配合 Cookie 才能实现。由于 Cookie 默认不支持跨域访问，所以，当涉及到前端跨域请求后端接口的时候，需要做很多额外的配置，才能实现跨域 Session 认证。

JWT认证：用户的信息通过 Token 字符串的形式，保存在客户端浏览器中。服务器通过还原 Token 字符串的形式来认证用户的身份。

JWT通常由三部分组成，分别是`Header(头部),Payload(有效载荷),Signature(签名)`，三者之间用`.`分隔。

- Payload 部分才是真正的用户信息，它是用户信息经过加密之后生成的字符串。
- Header 和 Signature 是安全性相关的部分，只是为了保证 Token 的安全性。

客户端收到服务器返回的 JWT 之后，通常会将它储存在 localStorage 或 sessionStorage 中。客户端每次与服务器通信，都要带上这个 JWT 的字符串，从而进行身份认证。推荐的做法是把 JWT 放在 HTTP 请求头的 Authorization 字段中。

```
Authorization: Bearer <token>
```

安装 JWT 相关的包。

- jsonwebtoken: 用于生成 JWT 字符串
- express-jwt: 用于将 JWT 字符串解析还原成 JSON 对象。

```
npm i jsonwebtoken express-jwt
```

为了保证 JWT 字符串的安全性，防止 JWT 字符串在网络传输过程中被别人破解，需要专门定义一个用于加密和解密的 secret 密钥:

1. 当生成 JWT 字符串的时候，需要使用 secret 密钥对用户的信息进行加密，最终得到加密好的 JWT 字符串
2. 当把 JWT 字符串解析还原成 JSON 对象的时候，需要使用 secret 密钥进行解密

jwt的`sign()`方法接收三个参数，参数1：用户的信息对象，参数2：加密的密钥，参数3：配置对象，可以配置当前token的有效期。

```js
const express = require('express');
const cors = require('cors');
const jsonwebtoken = require('jsonwebtoken');
const express_jwt = require('express-jwt')

const app = express();

app.use(cors());

const secretKey = 'zzwaka_123'

// 将 JWT 字符串还原解析为 JSON 对象
app.use(express_jwt({ secret: secretKey}).unless({ path: [/^\/api\//] }))

app.post('/api/login', (req, res) => {
  const user = req.body
  if (req.body.username === 'admin' && req.body.password === '123456') {
    return res.send({
      status: 400,
      message: '用户名或密码错误'
    })
  }
  // jwt的sign()方法接收三个参数，参数1：用户的信息对象，参数2：加密的密钥，参数3：配置对象，可以配置当前token的有效期。
  const tokenStr = jsonwebtoken.sign({
    username: user.username,
    password: user.password
    }, secretKey, {
      expiresIn: '1h'
  })
  res.send({
    status: 200,
    message: '登录成功',
    token: tokenStr
  })
})

app.get('admin/user', (req, res) => {
  res.send({
    status: 200,
    message: '获取用户信息成功',
    data: req.user
  })
})

app.use((err, req, res, next) => {
  if (err.name === 'UnauthorizedError') {
    return res.send({
      status: 401,
      message: '无效的token'
    })
  }
  res.send({
    status: 500,
    message: '未知错误'
  })
})

app.listen(3000, () => {
  console.log('Server is running at http://localhost:3000')
  }
)
```

