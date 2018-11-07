# monggodb 和 node.js使用
---
这几天搭建了个人的小博客，但是并没用到数据库，仅仅用到前后端的知识。所以我想把monggodb简单的记录一下，选择monggodb和node.js当然是因为他们简单上手。废话不多说。

## 一，mongogdb的连接
一般window启动monggodb默认是27107端口，所以可在node.js中这样写。

	// mongodb 连接🔗
	mongoose.connect('mongodb://localhost:27017/warehouse', { useNewUrlParser: true })

第一个参数是连接的数据库的表warehouse，

## 二，创建骨架
	var userSchema = mongoose.Schema({
		goodsId: String,   //货物号
		goodsName: String,	 //货物名
		goodsNum: String,	//货物数量
		goodsPrice: String,	  //货物单体价格
		goodsHoster: String,	//货物主人
		wareHouseId: String,	//货物存入的仓库
		state:String,       //出库状态
		time: String       //货物出库时间
	})

monggoose是已连接的数据库，其中userSchema是要存入的一些模板数据。

## 三，暴露出模板
	var model = {
		// 在此处扩展 model，例如：
		// Article: mongoose.model('Article', articleSchema),
		User: mongoose.model('User', userSchema)
	}

将模板model中的User暴露出去，用mongoose.model('User', userSchema)创建，就可以使用了。
在别的文件引入model，之后创建

    let userResiger =new model.User({
        goodsId: req.body.goodsId,
        goodsName: req.body.goodsName,
        goodsNum: req.body.goodsNum,
        goodsPrice: req.body.goodsPrice,
        goodsHoster: req.body.goodsHoster,
        wareHouseId: req.body.wareHouseId,
        state: "已入库"
    })


## 四，插入到数据库中
    userResiger.save((err, result) =>{
        if(err){
            console.log(err)
        }
        else {
            console.log("save monggodb success")
            res.json({
                success: 'insert'
            })
        }
    })
直接是用save方法

## 五，删除数据库中数据
    model.User.findOneAndRemove({_id:req.body.id}, err => {
        if (err)
            console.log(err)
        console.log('删除成功')
        res.json({
            success: true
        })
    })
第一个参数是数据库自己生成的_id

## 六，修改数据库
    model.User.findOneAndUpdate({_id : doc._id},
    req.body, 
    (err,res) => {
        if (err) {
            console.log("Error:" + err);
        }
    })
这里第一个参数同样，第二个参数是一个对象，里面包含你要修改的key和相应的value，只会修改到有的key，没有的不会修改。

## 七，查找数据库
	model.User.find({}, (err, doc) => {
        if(err) console.log(err)
        res.send(doc)
	})
find方法返回一个数组，第一个参数是对象，找到的数组为doc  

	model.User.findOne({email: userLogin.email}, 
	(err, doc)=>{
	if(doc){
	}	
	})

doc为一个对象不是数组。