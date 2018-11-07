#node
-----
##Http服务器
    var http = require('http');

    http.createServer(function(req,res){
    res.writeHead(200,{'Content-Type' : 'text/html'});
    res.write('<h1>Node.js</h1>');
    res.end('<p>Hello World!!</p>');
    }).listen(3000);

console.log("HTTP server is listening at port 3000.");
这段代码中，http.createServer 创建了一个 http.Server 的实例，将一个函数 作为 HTTP 请求处理函数。这个函数接受两个参数，分别是请求对象（ req ）和响应对象 （ res ）。在函数体内，res 显式地写回了响应代码 200 （表示请求成功），指定响应头为 'Content-Type': 'text/html'，然后写入响应体 ‘< h1>Node.js</h1>‘，通过 res.end 结束并发送。最后该实例还调用了 listen 函数，启动服务器并监听 3000 端口。



##GET请求URL获取参数
    var http = require('http');
    var url = require('url');
    var util = require('util');
 
    http.createServer(function(req, res){
    res.writeHead(200, {'Content-Type': 'text/plain'});
 
    // 解析 url 参数
    var params = url.parse(req.url, true).query;
    res.write("网站名：" + params.name);
    res.write("\n");
    res.write("网站 URL：" + params.url);
    res.end();
 
    }).listen(3000);


##Post请求内容
	var http = require('http');
	var querystring = require('querystring');
	 
	var postHTML = 
	  '<html><head><meta charset="utf-8"><title>菜鸟教程 Node.js 实例</title></head>' +
	  '<body>' +
	  '<form method="post">' +
	  '网站名： <input name="name"><br>' +
	  '网站 URL： <input name="url"><br>' +
	  '<input type="submit">' +
	  '</form>' +
	  '</body></html>';
 
    http.createServer(function (req, res) {
    var body = "";
    req.on('data', function (chunk) {
    body += chunk;
    });
    req.on('end', function () {
    // 解析参数
    body = querystring.parse(body);
    // 设置响应头部信息及编码
    res.writeHead(200, {'Content-Type': 'text/html; charset=utf8'});
 
    if(body.name && body.url) { // 输出提交的数据
        res.write("网站名：" + body.name);
        res.write("<br>");
        res.write("网站 URL：" + body.url);
    } else {  // 输出表单
        res.write(postHTML);
    }
    res.end();
    });
    }).listen(3000);