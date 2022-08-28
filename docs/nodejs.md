# Nodejs

## 1. 初始化一个项目

```bash
mkdir nodejs && cd nodejs && npm init -y
```

自动生成一个package.json文件

## 2. 安装依赖

```bash
npm install express 
```

Express 是一种保持最低程度规模的灵活 Node.js Web 应用程序框架

## 3. 创建一个入口文件

```bash
mkdir src && touch index.js 
```

在index.js 文件中写：

```js
const express = require('express')
const app = express()

app.get('/', (req, res) => {
  res.send('你好')
})

app.listen(3000, () => console.log('服务器已就绪'))
```

之后在package.json中设置命令并运行命令：

```json5
// package.json
{
  "name": "nodejs",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node ./src/index.js", // 创建的命令
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"
  }
}

运行命令

```bash
npm start
```

## 4. 路由

1. 创建routers文件夹及index.js文件

```bash
mkdir routers && touch index.js
```

1. 写路由

```js
const express = require('express');
const router = express.Router();

router.get('/', function(req, res) {
    res.send('Birds home page');
});

router.get('/about', function(req, res) {
    res.send('About birds');
});

module.exports = router;
```

1. 引入路由文件(在入口文件)写：

```js
const express = require('express')
const app = express()

const indexRouter = require('./routers');
app.use('/', indexRouter);

app.listen(3000, () => console.log('服务器已就绪'))

```

## 5. 创建静态文件

1. 安装模板依赖 ejs

```bash
npm install ejs
```

1. 入口文件改写：

```js
const express = require('express')
const app = express()

app.set('views', __dirname + '/views');//指定模板位置
app.set("view engine","ejs");

const indexRouter = require('./routers');
app.use('/', indexRouter);

app.listen(3000, () => console.log('服务器已就绪'))
```

1. 创建第一个ejs文件

```bash
cd views && touch index.ejs
```

```html
<!--index.ejs -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>测试</title>
</head>
<body>
    测试达成
</body>
</html>
```

4. 添加路由

在 routers/index.js 文件加入：

```js
router.get('/index', function(req, res) {
    res.render("index",{});
});

```

1. 重新启动服务，页面打开:localhost:3000/index 

案例：https://github.com/SunLxy/study_nodejs
