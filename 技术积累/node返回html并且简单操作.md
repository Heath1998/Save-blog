# Node设置html的显示

## 创建node服务器

	const express = require('express'),
	  app = express(),
	  // 原生的node创建http服务
	  server = require('http').createServer(app),
	  PORT = 8000,
	  users = [];

## 将当前文件设置为静态文件，这样可以通过http:XXXXXX/static/的形式访问

	app.use('/static', express.static(__dirname + '/static'));


## 返回当前文件的html

	res.sendFile(path);

## 开服务

	server.listen(PORT, () => {
	  console.log('启动8000');
	})

## html的引入，引入静态的文件index.js

	<script src="/static/js/index.js"></script>