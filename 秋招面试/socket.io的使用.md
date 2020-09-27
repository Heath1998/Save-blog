# socket.io的使用

### 基础
服务端

	var http=require("http");
	var express=require("express");//引入express
	var socketIo=require("socket.io");//引入socket.io
	var app=new express();
	var server=http.createServer(app);
	var io=new socketIo(server);//将socket.io注入express模块
	//客户端 1 的访问地址
	app.get("/client1",function (req,res,next) {
	    res.sendFile(__dirname+"/views/client1.html");
	});
	//客户端 2 的访问地址
	app.get("/client2",function (req,res,next) {
	    res.sendFile(__dirname+"/views/client2.html");
	});
	server.listen(8080);//express 监听 8080 端口，因为本机80端口已被暂用
	console.log('localhost::8080');
	//每个客户端socket连接时都会触发 connection 事件
	io.on("connection",function (clientSocket) {
	    console.log('on');
	    // socket.io 使用 emit(eventname,data) 发送消息，使用on(eventname,callback)监听消息
	    //监听客户端发送的 sendMsg 事件
	    clientSocket.on("sendMsg",function (data) {
	        console.log('off');
	        // data 为客户端发送的消息，可以是 字符串，json对象或buffer
	        // 使用 emit 发送消息，broadcast 表示 除自己以外的所有已连接的socket客户端。
	        clientSocket.broadcast.emit("receiveMsg",data);
	    })
	});

客户端

	 var socket = io.connect();//连接服务端，因为本机使用localhost 所以connect(url)中url可以不填或写 http://localhost
	    // 监听 receiveMsg 事件，用来接收其他客户端推送的消息
	 socket.on("receiveMsg",function (data) {
	        content.value+=data.client+":"+data.msg+"\r\n";
	 });
     // document触发发送
	 btn_send.addEventListener("click",function () {
	        var data={client:"客户端1",msg:sendMsg.value};
	 //给服务端发送 sendMsg事件名的消息
	 socket.emit("sendMsg",data);
	 content.value+=data.client+":"+data.msg+"\r\n";
	 sendMsg.value="";
	 });


### 关于命明空间room
可以服务端直接用of创建


	var namespace1=io.of("/namespace1");// 使用of("命名空间") 声明一个新的空间，不同空间下的socket是隔离的不能互相通信
	var namespace2=io.of("/namespace2");
	
	
	//每个客户端socket连接时都会触发 connection 事件
	namespace1.on("connection",function (clientSocket) {
	    // socket.io 使用 emit(eventname,data) 发送消息，使用on(eventname,callback)监听消息
	
	    //监听客户端发送的 sendMsg 事件
	    clientSocket.on("sendMsg",function (data,fn) {
	        // data 为客户端发送的消息，可以是 字符串，json对象或buffer
	
	        // 使用 emit 发送消息，broadcast 表示 除自己以外的所有已连接的socket客户端。
	        // to(房间名)表示给除自己以外的同一房间内的socket用户推送消息
	        clientSocket.broadcast.emit("receiveMsg",data);
	        fn({"code":0,"msg":"消息发生成功","namespace":"命名空间1"});
	    })
	});

客户端


 	var socket = io.connect("http://localhost:8080/namespace1");//连接服务端，因为本机使用localhost 所以connect(url)中url可以不填或写 http://localhost


room同时也可以直接用join加入房间

	//每个客户端socket连接时都会触发 connection 事件
	io.on("connection",function (clientSocket) {
	    // socket.io 使用 emit(eventname,data) 发送消息，使用on(eventname,callback)监听消息
	
	    //加入房间
	    clientSocket.on("joinRoom",function (data,fn) {
	        clientSocket.join(data.roomName); // join(房间名)加入房间
	        fn({"code":0,"msg":"加入房间成功","roomName":data.roomName});
	    });
	    //退出 离开房间
	    clientSocket.on("leaveRoom",function (data,fn) {
	        clientSocket.leave(data.roomName);//leave(房间名) 离开房间
	        fn({"code":0,"msg":"已退出房间","roomName":data.roomName});
	    });
	    //监听客户端发送的 sendMsg 事件
	    clientSocket.on("sendMsg",function (data,fn) {
	        // data 为客户端发送的消息，可以是 字符串，json对象或buffer
	
	        // 使用 emit 发送消息，broadcast 表示 除自己以外的所有已连接的socket客户端。
	        // to(房间名)表示给除自己以外的同一房间内的socket用户推送消息
	        clientSocket.broadcast.to(data.roomName).emit("receiveMsg",data);
	        fn({"code":0,"msg":"消息发生成功"});
	    })
	});