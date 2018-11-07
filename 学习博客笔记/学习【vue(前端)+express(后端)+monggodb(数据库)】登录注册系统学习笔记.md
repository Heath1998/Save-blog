#学习【vue(前端)+express(后端)+monggodb(数据库)】登录注册系统学习笔记

-----
##vue前端注意事项
1.记得加入路由守卫。  
在需要密码才能进入的路由上加上requireAuth属性

    {
      path: '/',
      name: 'Hello',
      component: Hello,
      meta:{
        requireAuth:true
      }
    },

再在下面加入路由守卫  

	// 验证 token，存在才跳转
	router.beforeEach((to, from, next) => {
		let token = localStorage.getItem('token')
		if(to.meta.requireAuth) {
			if(token) {
				next()
			} else {
				next({
					path: '/login',
					query: { redirect: to.fullPath }
				})
			}
		} else {
			next()
		}
	})
当路由跳转时，判断是否是跳入有meta属性的路由，若是则判断是否存在token，存在就跳转。  
不存在就跳回登录界面。


2.设置请求头和设置axios的拦截响应  

	//修改请求的类型为json
	const instance = axios.create();
	instance.defaults.headers.post['Content-Type'] = 'application/json'

将token加入Authorization中

	//设置请求头
	axios.interceptors.request.use = instance.interceptors.request.use
	instance.interceptors.request.use(config => {
		if(localStorage.getItem('token')) {
			config.headers.Authorization = `token ${localStorage.getItem('token')}`
				
		}
		return config
	}, err => {
		return Promise.reject(err)
	})
axios拦截响应
	
	// axios拦截响应
	instance.interceptors.response.use(response => {
		if(response.code == 401)
			console.log('token过期')
		return response
	}, err => {
		return Promise.reject(err)
	})

暴露出去

	export default {
	// 用户注册
	userRegister(data) {
		return instance.post('/api/register', data)
	},
	// 用户登录
	UserLogin(data) {
		return instance.post('/api/login', data)
	},
	// 获取用户
	getUser() {
		return instance.get('/api/user')
	},
	// 删除用户
	delUser(data) {
		return instance.post('/api/delUser', data)
	}
	}

##express注意事项
1.使用token  
引入const jwt = require('jsonwebtoken')  
创造token
	
	//创造token，有效期为10s，方便观察	
	module.exports = function (name) {
	    const token = jwt.sign({
	        name:name
	    }, 'secret', {expiresIn: '10s'})
	    return token
	}

检查token要用在用户界面的使用
	
	module.exports = function (req, res, next) {
	    //console.log(req.headers)
	    let token = req.headers['authorization'].split(' ')[1]
	    // 解构 token，生成一个对象 { name: xx, iat: xx, exp: xx }
	    let decoded = jwt.decode(token, 'secret')
		// 监测 token 是否过期
	    if(token && decoded.exp <= Date.now() /1000){
	        return res.json({
	            code:401,
	            message:"token过期,请重新登录"
	        })
	    }   
	    next()
	}

2。之后就是通过router写api接口给前端调用，这一部分较简单就不演示了。

##数据库mongondb

	// mongodb 连接🔗
	mongoose.connect(config.mongodb, { useNewUrlParser: true })

	var userSchema = mongoose.Schema({
	email: String,
	password: String,	
	recheck: String,
	token: String,
	create_time: Date
	})

之后userSchema的骨架一样的数据再操作以下存入数据库