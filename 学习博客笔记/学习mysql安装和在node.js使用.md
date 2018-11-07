# 学习mysql安装和在node.js使用
-----

## 一，安装window上mysql
[https://www.cnblogs.com/xsmile/p/7753984.html](https://www.cnblogs.com/xsmile/p/7753984.html)

## 二，node连接数据库
首先引入mysql，

	var connection = mysql.createConnection({     
	  host     : 'localhost',       
	  user     : 'root',              
	  password : 'fan******',       
	  port: '3306',                   
	  database: 'runoob' 
	}); 
数据库连接

    connection.connect();
数据库断开

	connection.end();

## 三，创建新的数据库
mysql通用语法创建一个新的数据库

	CREATE TABLE IF NOT EXISTS `runoob_tbl`(
	   `runoob_id` INT UNSIGNED AUTO_INCREMENT,
	   `runoob_title` VARCHAR(100) NOT NULL,
	   `runoob_author` VARCHAR(40) NOT NULL,
	   `submission_date` DATE,
	   PRIMARY KEY ( `runoob_id` )
	)ENGINE=InnoDB DEFAULT CHARSET=utf8;

使用node.js创建trybiao234表的话代码如下：
	
	var two = '`trybiao234`'
	var  sql = "CREATE TABLE IF NOT EXISTS" + two + "(\
	    `runoob_id` INT UNSIGNED AUTO_INCREMENT,\
	    `runoob_title` VARCHAR(100) NOT NULL,\
	    `runoob_author` VARCHAR(40) NOT NULL,\
	    `submission_date` DATE,\
	    PRIMARY KEY ( `runoob_id` )\
	 )ENGINE=InnoDB DEFAULT CHARSET=utf8;\
	"
	connection.query(sql,function (err, result) {
	        if(err){
	          console.log('[DELETE ERROR] - ',err.message);
	          return;
	        }        
	 		console.log(result)
	});

## 四，向表中插入一个数据
mysql向数据库中的表插入一个数据，mysql语法

	INSERT INTO table_name ( field1, field2,...fieldN )
	         VALUES
	         ( value1, value2,...valueN );

node的写法

	var  addSql = 'INSERT INTO websites(Id,name,url,alexa,country) VALUES(0,?,?,?,?)';
	var  addSqlParams = ['菜鸟工具', 'https://c.runoob.com','23453', 'CN'];
	//增
	connection.query(addSql,addSqlParams,function (err, result) {
	        if(err){
	         console.log('[INSERT ERROR] - ',err.message);
	         return;
	        }        
	
	       console.log('INSERT ID:',result);        
	      
	});

## 五，删除表中一个数据
node的写法

	var delSql = 'DELETE FROM websites where id=?;
	//删
    var dId = [6]
	connection.query(delSql, dId, function (err, result) {
	        if(err){
	          console.log('[DELETE ERROR] - ',err.message);
	          return;
	        }        
	 
	       console.log('--------------------------DELETE----------------------------');
	       console.log('DELETE affectedRows',result.affectedRows);
	       console.log('-----------------------------------------------------------------\n\n');  
	});

## 六，更新数据
node写法

	var modSql = 'UPDATE websites SET name = ?,url = ? WHERE Id = ?';
	var modSqlParams = ['菜鸟移动站', 'https://m.runoob.com',6];
	//改
	connection.query(modSql,modSqlParams,function (err, result) {
	   if(err){
	         console.log('[UPDATE ERROR] - ',err.message);
	         return;
	   }        
	  console.log('UPDATE affectedRows',result);
	
	});

## 七，查找数据
node写法

	var  sql = 'SELECT * FROM websites';
	//查
	connection.query(sql,function (err, result) {
	        if(err){
	          console.log('[SELECT ERROR] - ',err.message);
	          return;
	        }
	       console.log(result);
	
	});
查着全部数据

	var  sql = 'SELECT * FROM websites WHERE name=?';
	var two =['Google']
	//查
	connection.query(sql,two,function (err, result) {
	        if(err){
	          console.log('[SELECT ERROR] - ',err.message);
	          return;
	        }
	       console.log(result);
	       
	});

用WHERE来寻找