# 关于如何使用esLint
-------

### eslint是一个对代码进行静态检查，对代码的规范有显著帮助。
如何使用：    

首先全局下载

	npm install -g eslint
	// 之后就可以使用了
	
	eslint --init
	// 进行初始化后，会有许多选择，按自己的配置点确定就好

	// 之后会生成一个 .eslintrc.js的文件可以在里面配置一些东西
	// 例如在rules下

	"rules": {
		'semi': ['error', 'always'],    //结尾总是要双引号
		'indent': ['error', 4]    //就是要四个空格为一个缩进
	}

	之后就是安装各种包npm install
	在package.json下的scripts增加一条命令"lint": "eslint --fix src"
	这样他就会自动修复src文件下js的常见错误了。

	如果是vue项目的话，同样再命令添加
    "lint": "eslint --fix --ext .js,.vue src"
	自动检查src下的vue和js文件