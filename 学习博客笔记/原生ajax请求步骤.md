（18.12.27）
# 原生ajax请求步骤

（1）创建XMLHttpRequest对象

	var xhr = new XMLHttpRequest();
(2)设置请求参数

	xhr.open(请求方式, 请求地址, 异步或同步);
	是post请求的话要设置请求头信息
	xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

(3)设置回调函数，当收到返回信息，要执行的操作

	xhr.onreadystatechange = function() {
	if(xhr.readyState == 4 && xhr.status == 200)
		{
			//操作
			}
	}

（4）发送请求
	
	xhr.send()