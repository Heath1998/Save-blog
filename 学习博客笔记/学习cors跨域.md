# 学习通过后端配置cors游览器解决跨域访问  

现在的游览器ie10以上的都可以浏览器直接发出CORS请求。体来说，就是在头信息之中，增加一个Origin字段。然后node后端   

	router.get('/getData', function(req, res, next) {
	  //设置允许跨域请求
	    var reqOrigin = req.header("origin");
	 
	  if(reqOrigin !=undefined && reqOrigin.indexOf("http://localhost:8080") > -1){
	  //设置允许 http://localhost:3000 这个域响应
	    res.header("Access-Control-Allow-Origin", "http://localhost:8080");
	    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
	    res.header("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
	  }
	  res.json(200, {str: "cors跨域成功"});
	 
	});

对前端的请求过滤若允许请求就在回复头加Access-Control-Allow-Origin和Access-Control-Allow-Methods字段，游览器会主动识别获取后端传过来的数据。