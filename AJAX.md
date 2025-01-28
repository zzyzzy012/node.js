# AJAX

## 1.AJAX 简介

**AJAX (Asynchronous JavaScript and XML) ，异步的JS和XML。**允许服务器异步加载，而不需要刷新页面。

**XML (Extensible Markup Language)可扩展标记语言。**AJAX用来存储和传输数据，标签都是自定义的，而HTML更多是网页展示数据，标签是预定义的。现在更多被JSON取代。

AJAX的优缺点

1. 不需要刷新页面即可与服务器通信
2. 可以根据用户事件来刷新部分网页
3. 没有浏览历史，不能回退🔙
4. 存在跨域问题
5. SEO不友好

## 2.HTTP协议

HTTP (HyperText Transfer Protocol)超文本传输协议，规定了浏览器和服务器之前的通信规则。

**Request，请求体：**客户端向服务器发送数据。

Request Line，请求行，HTTP method (e.g., `GET`, `POST`), the resource path, and the HTTP version.`GET /index.html HTTP/1.1`

Headers:请求头，键值对方式。

```
example
Host: www.example.com
User-Agent: Mozilla/5.0
Accept-Language: en-US
```

Body:请求体，从客户端给服务器发送数据。

```
{
  "username": "john_doe",
  "password": "12345"
}
```

**Response，响应体：**服务器向客户端发送数据。

Request Line:响应头，Contains the HTTP version, status code, and a status message.`HTTP/1.1 200 OK`

Headers:响应头，键值对方式。

Body:响应体，数据可能是`HTML、JSON...`

**HTTP方法：**

- GET
- POST：提交数据以供服务器处理
- PUT:更新服务器存在的资源
- DELETE
- PATCH:部分更新服务器资源
- HEAD:跟GET类似，但只检索HEADERS，而不检索BODY。

**HTTP Status Codes：**

- **200 OK**
- **301 Moved Permanently**: The resource has been permanently moved to a new URL.
- **400 Bad Request**: The server could not understand the request due to invalid syntax.
- **401 Unauthorized**: Authentication is required and has either failed or not been provided.
- **404 Not Found**: The requested resource could not be found on the server.
- **500 Internal Server Error**: A generic error indicating the server has encountered an unexpected condition.

## 3.XMLHttpRequest

**XMLHttpRequest：**浏览器的内置对象，用于向服务器发送 HTTP 请求。

**常用API：**

`xhr.open(method, url, async, user, password)`

- **method**: The HTTP request method (e.g., `"GET"`, `"POST"`).
- **url**: The URL to which the request is sent.可在后面携带参数，参数格式为`?key1=val1&key2=val2`。
- **async**: `true` or `false`.
- **user | password** (optional): The username | password for authentication.

```js
xhr.open('GET', 'http://127.0.0.1:80/server', true);
xhr.open('GET', 'http://127.0.0.1:80/server?user=admin&pwd=123456')
```

`xhr.send()`向服务器发送数据。

`xhr.setRequestHeader(header, value)`设置请求头。

```js
xhr.setRequestHeader('Content-Type', 'application/json');
```

`xhr.getResponseHeader(header)`返回特定响应头的值。

```js
const contentType = xhr.getResponseHeader('Content-Type');
```

`xhr.getAllResponseHeaders()`返回所有响应头的值，以字符串的形式。

```
const headers = xhr.getAllResponseHeaders();
```

`xhr,abort()`取消仍在进行中的请求。

`xhr.onreadystatechange`处理服务器返回的结果。每当 readyState 发生变化时触发的事件处理程序。readyState 的值可以是 0 到 4，代表请求生命周期的不同阶段。

1. 0：UNSENT（请求未初始化）
2. 1：OPENED（请求已打开）
3. 2：HEADERS_RECEIVED（已收到标头）
4. 3：LOADING（响应正在进行中）
5. 4：DONE（请求已完成）

Status

1xx: Informational Responses:请求已被接收并正在处理。

2xx: Successful Responses

3xx: Redirection Responses

4xx: Client Error Responses:表示客户端的请求有错误。

5xx: Server Error Responses：表明服务器未能满足有效请求。

```js
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log(xhr.responseText);
  }
};
```

`xhr.responseText`返回作为字符串格式的响应数据。

`xhr.responseXML`返回XML事件对象的响应数据。

`xhr.timeout`请求超时前的时间（以毫秒为单位）。默认值为 0，表示无超时。

```js
xhr.timeout = 5000;
```

`xhr.ontimeout`请求超时时触发事件处理程序。

```js
xhr.ontimeout = function() {
  console.log('The request timed out');
};
```

## 4.post请求

server.js借助express框架搭建服务器。

```js
const express = require('express');
const app = express();

app.get('/server', (req, res) => {
  // 设置允许跨域
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  res.send('GET request received');
})

app.post('/server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  res.send('POST request received');
})

app.listen(80, () => {
  console.log('Server is running at http://127.0.0.1:80');
});
```

```js
const reqBtn = document.querySelector('.req-btn')
    const resultDiv = document.querySelector('.result')
    reqBtn.addEventListener('click', () => {
      const xhr = new XMLHttpRequest()
      // 初始化，设定请求方法和URL
      xhr.open('POST', 'http://127.0.0.1:80/server')
      // 发送请求，post请求设置请求体，chrome在Request Payload中查看
      // xhr.send('name=张三&age=20')
      xhr.send('{"name": "张三", "age": 20}')
      // 事件绑定 处理服务器返回的结果
      xhr.onreadystatechange = function() {
        if (xhr.readyState === 4) {
          if (xhr.status >= 200 && xhr.status < 300) {
            console.log(xhr.readyState)
            console.log(xhr.status)
            console.log(xhr.statusText)
            console.log(xhr.getAllResponseHeaders())
            console.log(xhr.responseText)
            console.log(xhr.responseType)
            console.log(xhr.responseURL)
            console.log(xhr.responseXML)
            resultDiv.innerHTML = xhr.responseText
          }
        }
      }
    })
```

设置响应头

server.js

```js
app.all('/server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  // res.setHeader('Access-Control-Allow-Methods', 'POST, GET, OPTIONS, PUT, DELETE')
  res.send('POST request received');
})
```

```js
const reqBtn = document.querySelector('.req-btn')
    const resultDiv = document.querySelector('.result')
    reqBtn.addEventListener('click', () => {
      const xhr = new XMLHttpRequest()
      // 初始化，设定请求方法和URL
      xhr.open('POST', 'http://127.0.0.1:80/server')
      // 设置请求头，content-type指定请求体格式 
      xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
      // 设置请求头，自定义请求头
      xhr.setRequestHeader('name', 'zhangsan')
      // 发送请求，post请求设置请求体，chrome在Request Payload中查看
      xhr.send('name=zhangsan&age=20')
      // xhr.send('{"name": "张三", "age": 20}')
      // 事件绑定 处理服务器返回的结果
      xhr.onreadystatechange = function() {
        if (xhr.readyState === 4) {
          if (xhr.status >= 200 && xhr.status < 300) {
            resultDiv.innerHTML = xhr.responseText
          }
        }
      }
    })
```

## 5.JSON请求

浏览器向服务器请求JSON格式数据。

```js
app.all('/json-server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  const data = {
    name: 'John',
    age: 30,
    city: 'New York',
    phone: '123-456-7890',
    email: 'john@example.com',
  }
  const dataStr = JSON.stringify(data);
  res.send(dataStr);
})
```

JSON响应，`xhr.responseType = 'json'`。

```js
    const reqBtn = document.querySelector('.req-btn')
    const resultDiv = document.querySelector('.result')
    reqBtn.addEventListener('click', () => {
      const xhr = new XMLHttpRequest()
      xhr.responseType = 'json'
      // 初始化，设定请求方法和URL
      xhr.open('GET', 'http://127.0.0.1:80/json-server')
      xhr.send()
      // 事件绑定 处理服务器返回的结果
      xhr.onreadystatechange = function() {
        if (xhr.readyState === 4) {
          if (xhr.status >= 200 && xhr.status < 300) {
            // 自动转换
            resultDiv.innerHTML = xhr.response.name
          }
        }
      }
    })
```

## 6.xhr常用API

设置超时时间`xhr.timeout = xxx`

超时的回调函数`xhr.tmieout = () => {}`

网络错误的回调函数`xhr.onerr = () => {}`

```js
const resultDiv = document.querySelector('.result')
resultDiv.addEventListener('click', () => {
  const xhr = new XMLHttpRequest()
  // 设置超时时间
  xhr.timeout = 2000
  // 超时的回调函数
  xhr.timeout = function() {
    resultDiv.innerHTML = '请求超时'
  }
  // 服务器响应失败的回调函数
  xhr.onerror = function() {
    resultDiv.innerHTML = '服务器响应失败'
  }
  xhr.open('GET', 'http://127.0.0.1:80/delay')
  xhr.send()
  xhr.onreadystatechange = function() {
    if (xhr.readyState === 4) {
      if (xhr.status >= 200 && xhr.status < 300) {
        resultDiv.innerHTML = xhr.response
      }
    }
  }
})
```

`xhr.abort()`取消请求

取消重复请求通过设置一个标识符。

```js
    const btn = document.querySelector('.btn');
    // 定义标识符
    let isSending = false
    btn.addEventListener('click', function() {
      const xhr = new XMLHttpRequest();
      if (isSending) xhr.abort()  // 取消重复请求
      isSending = true
      xhr.open('GET', 'http://127.0.0.1:80/repeat')
      xhr.send()
      xhr.onreadystatechange = () => {
        if (xhr.readyState === 4) {
          isSending = false
          if (xhr.status >=200 && xhr.status < 300) {
            console.log(xhr.responseText)
          }
        }
      }
    })
```

## 7.三方工具

### 7.1 jquery

```js
// jquery
app.get('/jquery-server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  // res.send('jQuery request received');
  const data = {
    name: 'John',
  }
  res.send(JSON.stringify(data))
})
```

```js
<body>
  <button>GET</button>
  <button>POST</button>
  <button>通用方法</button>
  <script>
    // GET 请求
    $("button").eq(0).click(function() {
      $.get('http://127.0.0.1:80/jquery-server', {a:100,b:200}, function(data) {
        console.log(data)
      }, 'json')
    })

    // POST 请求
    $("button").eq(1).click(function () {
      $.post('http://127.0.0.1:80/jquery-server', { a: 100, b: 200 }, function (data) {
        console.log(data);
      });
    });

    // 通用方法
    $("button").eq(2).click(function () {
        $.ajax({
          url: 'http://127.0.0.1:80/jquery-server',
          data: { a: 100, b: 200 },
          // 响应体结果
          dataType: 'json',
          type: 'GET',
          timeout: 2000,
          success: function (data) {
            console.log(data)
          },
          error: function (xhr, status, error) {
            console.log(xhr.status + '|' + status + '|' + error)
          }
        })
      })
  </script>
</body>
```

### 7.2 axios

```js
// axios
app.get('/axios-server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  const data = {
    name: 'John',
  }
  res.send(JSON.stringify(data))
})
// POST route for axios-server
app.post('/axios-server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Access-Control-Allow-Headers', '*')
  const data = {
    name: 'John',
  }
  res.send(JSON.stringify(data))
});
```

```js
<body>
  <button>GET</button>
  <button>POST</button>
  <button>AJAX</button>
  <script>
    const btn = document.querySelectorAll('button')
    // 配置 baseUrl
    axios.defaults.baseURL = 'http://127.0.0.1:80'
    // GET Request
    btn[0].addEventListener('click', () => {
      axios.get('/axios-server', {
        // url 参数
        params: {
          name: 'Alice',
          age: 20,
          sex: 'female'
        },
        // 请求头
        headers: {
          price: '100USD',
        },
      })
    })

    // POST Request
    btn[1].addEventListener('click', () => {
      axios.post('/axios-server',
      {
        // 请求体数据
          movie: 'Stranger Things'
      },
      {
        // 请求头
        headers: {
          hobby: 'music', // Custom header
        },
      })
    });

    // 通用请求
      btn[2].addEventListener('click', () => {
        axios({
          method: 'get',
          url: '/axios-server',
          headers: {
            hobby: 'music', // Custom header
          },
          data: {
            movie: 'Stranger Things'
          }
        })
      });
  </script>
</body>
```

### 7.3 fetch全局工具

```js
// fetch
app.post('/fetch-server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.send('Fetch request received')
})
```

```js
  <button>Fetch Request</button>
  <script>
    const btn = document.querySelector('button');
    btn.addEventListener('click', () => {
      fetch('http://127.0.0.1:80/fetch-server', {
        method: 'POST',
        headers: {
          authorization: 'Bearer 1234567890',
          like: 'apples',
        },
        body: {
          name: 'John',
          age: 30,
          phone: '123-456-7890',
        }
      })
       .then(response => response.json())
       .then(data => console.log(data))
       .catch(error => console.error(error));
       });
  </script>
```

## 8.跨域

**同源策略（Same-Origin Policy）** 是浏览器的一种核心安全机制，用于限制一个网页如何与来自不同源的资源进行交互。

“同源”是指两个 URL 的 **协议（Protocol）**、**域名（Domain）** 和 **端口（Port）** 完全相同。如果其中任何一个部分不同，则被视为 **跨源（Cross-Origin）**。

同源策略的主要目的是保护用户的隐私和安全，防止恶意网站通过脚本访问其他网站的数据。

### 8.1 JSONP

JSONP（JSON with Padding）是一种用于解决跨域问题的技术。由于浏览器的同源策略（Same-Origin Policy），AJAX 请求通常只能发送到与当前页面同源的服务器。

**原理：利用 `<script>` 标签的跨域特性。浏览器允许加载来自不同域的 JavaScript 文件，JSONP 正是利用这一点来实现跨域请求。**

1. **动态创建 <script> 标签**：
   客户端动态创建一个 `<script>` 标签，并将其 `src` 属性指向目标服务器的 URL。这个 URL 通常包含一个回调函数名作为查询参数。
2. **服务器返回 JavaScript 代码**：
   服务器接收到请求后，会将数据包装在回调函数中返回。
3. **客户端定义回调函数**：
   客户端需要提前定义一个与服务器返回的回调函数同名的函数。当服务器返回的 JavaScript 代码被执行时，回调函数会被调用，并传入服务器返回的数据。

**缺点：**

1. **仅支持 GET 请求**：由于 JSONP 是通过 `<script>` 标签实现的，因此只能发送 GET 请求，无法使用 POST、PUT 等其他 HTTP 方法。
2. **安全性问题**：JSONP 依赖于服务器返回的 JavaScript 代码，如果服务器返回的代码被恶意篡改，可能会导致安全问题（如 XSS 攻击）。
3. **错误处理困难**：JSONP 没有像 AJAX 那样的错误处理机制，如果请求失败，很难捕获和处理错误。

```js
const express = require('express')
const app = express()

// const data = {
//   type: '车-小米SU7',
//   price: '20万',
// }
// console.log(data)
// handle(data)

// JSONP
app.get('/jsonp-server', (req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*')
  res.setHeader('Aceess-Control-Allow-Headers', '*')
  const data = {
    user: 'Tom',
  }
  const dataStr = JSON.stringify(data)
  res.end(`handle(${dataStr})`)
})

app.listen(3000, () => {
  console.log('Server is running at http://127.0.0.1:3000')
})
```

```js
  <div class="result"></div>
  <script>
    const res = document.querySelector('.result');
    const handle = (str) => {
      res.innerHTML = str
    }
  </script>
  <!-- <script src="./app.js"></script> -->
   <script src="http://127.0.0.1:3000/jsonp-server"></script>
```

### 8.2 CORS

CORS（Cross-Origin Resource Sharing，跨域资源共享）是一种现代浏览器支持的跨域解决方案。它允许服务器明确指定哪些外部域可以访问其资源，从而解决了浏览器的同源策略限制。支持所有 HTTP 方法（如 GET、POST、PUT、DELETE 等）。

#### 8.2.1 CORS 的工作原理

##### 1. 同源策略与跨域请求

浏览器的同源策略会阻止从一个域向另一个域发送 AJAX 请求（除非目标服务器明确允许）。CORS 通过在 HTTP 头中添加特定的字段，允许服务器声明哪些外部域可以访问其资源。

##### 2. 简单请求与非简单请求

CORS 将请求分为两类：

- **简单请求**：满足以下条件的请求：
  - 使用 GET、POST 或 HEAD 方法。
  - 仅包含以下头信息：`Accept`、`Accept-Language`、`Content-Language`、`Content-Type`（仅限 `application/x-www-form-urlencoded`、`multipart/form-data` 或 `text/plain`）。
    - `Content-Type`：指示客户端发送给服务器的数据的媒体类型（MIME 类型）。它告诉服务器如何解析请求体中的数据。
- **非简单请求**：不满足上述条件的请求（如使用 PUT、DELETE 方法，或包含自定义头信息）。

对于非简单请求，浏览器会先发送一个 **预检请求（Preflight Request）**，使用 `OPTIONS` 方法询问服务器是否允许实际请求。

1.`application/x-www-form-urlencoded`：通常用于 HTML 表单提交。数据会被编码为键值对，格式类似于 URL 查询字符串。

- 数据以 `key=value` 的形式传递，多个键值对用 `&` 连接。
- 空格会被编码为 `+`，特殊字符会被 URL 编码（如 `%20` 表示空格）。
- 适用于简单的表单数据提交。

```js
请求体：
name=John+Doe&age=30

对应的 Content-Type：
Content-Type: application/x-www-form-urlencoded
```

2.`multipart/form-data`：这种 `Content-Type` 通常用于上传文件或提交包含二进制数据的表单。数据会被分成多个部分（parts），每个部分都有自己的头和体。

- 数据被分成多个部分，每个部分用唯一的 `boundary` 分隔。
- 每个部分可以包含文本或二进制数据。
- 适用于文件上传或复杂表单提交。

```js
请求体：
--boundary
Content-Disposition: form-data; name="name"

John Doe
--boundary
Content-Disposition: form-data; name="avatar"; filename="photo.jpg"
Content-Type: image/jpeg

(二进制文件数据)
--boundary--

对应的 Content-Type：
Content-Type: multipart/form-data; boundary=boundary
```

3.`text/plain`：这种 `Content-Type` 用于发送纯文本数据，不进行任何编码或格式化。

```js
请求体：
Hello, this is plain text.

对应的 Content-Type：
Content-Type: text/plain
```

##### 3. CORS 的 HTTP 头

CORS 的核心是通过 HTTP 头来实现的。以下是一些关键的 HTTP 头：

- **请求头**：
  - `Origin`：表示请求的来源域（由浏览器自动添加）。
  - `Access-Control-Request-Method`：在预检请求中，表示实际请求的 HTTP 方法。
  - `Access-Control-Request-Headers`：在预检请求中，表示实际请求的自定义头信息。
- **响应头**：
  - `Access-Control-Allow-Origin`：指定允许访问资源的域（如 `*` 表示允许所有域）。
  - `Access-Control-Allow-Methods`：指定允许的 HTTP 方法（如 `GET, POST, PUT`）。
  - `Access-Control-Allow-Headers`：指定允许的自定义头信息。
  - `Access-Control-Allow-Credentials`：指定是否允许发送凭据（如 Cookies）。
  - `Access-Control-Max-Age`：指定预检请求的缓存时间（单位：秒）。

#### 8.2.2 在 Node.js 中使用 `cors` 包

使用 npm 或 yarn 安装 `cors` 包：

```bash
npm install cors
# 或
yarn add cors
```

以下是一个使用 `cors` 的简单示例：`cors()` 默认允许所有域访问资源。

```js
const express = require('express');
const cors = require('cors');

const app = express();

// 使用默认配置启用 CORS
app.use(cors());

app.get('/api/data', (req, res) => {
    res.json({ message: 'This is CORS-enabled for all origins!' });
});

app.listen(3000, () => {
    console.log('Server is running on http://localhost:3000');
});
```

**自定义配置**

允许特定域访问

```js
const corsOptions = {
    origin: 'https://example.com', // 只允许 example.com 访问
    optionsSuccessStatus: 200 // 预检请求的成功状态码
};

app.use(cors(corsOptions));
```

允许多个域访问

```js
const allowedOrigins = ['https://example.com', 'https://another-domain.com'];

const corsOptions = {
    origin: (origin, callback) => {
        if (allowedOrigins.includes(origin)) {
            callback(null, true); // 允许访问
        } else {
            callback(new Error('Not allowed by CORS')); // 拒绝访问
        }
    }
};

app.use(cors(corsOptions));
```

允许发送凭据（如 Cookies）

```js
const corsOptions = {
    origin: 'https://example.com',
    credentials: true // 允许发送凭据
};

app.use(cors(corsOptions));
```

配置预检请求缓存

```js
const corsOptions = {
    origin: 'https://example.com',
    methods: 'GET,POST,PUT,DELETE', // 允许的 HTTP 方法
    allowedHeaders: 'Content-Type,Authorization', // 允许的自定义头
    maxAge: 86400 // 预检请求缓存时间（24 小时）
};

app.use(cors(corsOptions));
```

**动态配置**

可以根据请求动态调整 CORS 配置。例如，根据请求的路径或头信息决定是否允许跨域访问：

```js
app.use((req, res, next) => {
    const corsOptions = {
        origin: req.path.startsWith('/public') ? '*' : 'https://example.com'
    };
    cors(corsOptions)(req, res, next);
});
```

------

#### CORS 的注意事项

1. **安全性**：
   - 不要随意设置 `Access-Control-Allow-Origin: *`，除非你明确允许所有域访问。
   - 如果允许发送凭据（如 Cookies），`Access-Control-Allow-Origin` 不能为 `*`，必须指定具体的域。
2. **预检请求**：
   - 对于非简单请求，浏览器会先发送预检请求。确保服务器正确处理 `OPTIONS` 请求。
3. **缓存**：
   - 使用 `Access-Control-Max-Age` 可以减少预检请求的次数，但要注意缓存时间不宜过长。
4. **兼容性**：
   - CORS 在现代浏览器中广泛支持，但在一些老旧浏览器中可能存在问题。