#快餐店收银系统Pos学习笔记
##第一节mockplus
这是一个产品经理使用的软件，可以大概地做出网页样式。

##第二节安装vue-cli环境

	mpm install vue-cli -g
全局下安装vue-cli脚手架  


	vue init webpack
进入目录下后输入上面的命令，可以初始化当期文件夹，采用webpack模板
 
之后在component下新建一个pos.vue，之后配置相对的路由路径router

##第三节图标选择
可以登录这个网站进行获取，http://www.iconfont.cn

##第四节编写独立的侧边栏导航组件
即编写悬浮在左边的菜单栏首先创建一个leftNav.vue的组键，写好相应的html和css样式，  
记得要float:left;让它左浮动。之后引入App.vue中，  <leftNav></leftNav>这样就可以直接引入。
##第五节Element的使用
Element是一套为开发者、设计师和产品经理准备的基于Vue2.0的组件库，提供了配套设计资源，帮助你的网站快速成型  

	npm install element-ui --save
下载到当前工程下。
之后在项目中引入可以在main.js下进行操作  

	import ElementUI from 'element-ui'
	import 'element-ui/lib/theme-chalk/index.css'
	 
	Vue.use(ElementUI)
这样便可以使用组件库了。

用Element的el-row的布个局  
安装好，先做个简单的布局小试牛刀，这里作两栏布局，Element支持用24栏的形式进行布局。

    <div>
        <el-row >
            <el-col :span='7'>
            我是订单栏
            </el-col>
            <!--商品展示-->
            <el-col :span="17">
             我是产品栏
            </el-col>
        </el-row>
    </div>

但是使用组件是无法设置高度100%的。  
所以用javascript来实现，
 
		mounted:function(){
		      var orderHeight=document.body.clientHeight;
		      document.getElementById("order-list").style.height=orderHeight+'px';
		  },  
使用到了vue构造器里的钩子函数。

##第六节element布局（一）
el-tabs标签页组件  
用Element里提供的el-tabs组件可以快速制作我们的tabs标签页效果，具体使用方法可以到Element的官网查看API。  
基本用法很简单，可以直接在模板中引入<el-tabs>标签，标签里边用<el-tab-pane>来代表每个每个标签页。  

	<el-tabs>
	      <el-tab-pane label="点餐">
	       点餐   
	      </el-tab-pane>
	      <el-tab-pane label="挂单">
	      挂单
	      </el-tab-pane>
	      <el-tab-pane label="外卖">
	      外卖
	     </el-tab-pane>
	</el-tabs>
相当于一个多选项卡列表。  

el-table组件制作表格  
	
	<el-table :data="tableData" border show-summary style="width: 100%" >
	 
	    <el-table-column prop="goodsName" label="商品"  ></el-table-column>
	    <el-table-column prop="count" label="量" width="50"></el-table-column>
	    <el-table-column prop="price" label="金额" width="70"></el-table-column>
	    <el-table-column  label="操作" width="100" fixed="right">
	        <template slot-scope="scope">
	            <el-button type="text" size="small">删除</el-button>
	            <el-button type="text" size="small">增加</el-button>
	 
	        </template>
	    </el-table-column>
	</el-table>

建立一个表数据为tableData，列项为goodsName，count，price.最后一项是添加了两个按钮。

再在下面放三个el-button 按钮组件  

	<el-button type="warning" >挂单</el-button>
	<el-button type="danger" >删除</el-button>
	<el-button type="success" >结账</el-button>
type设置颜色。


##第七节Element快速布局（二）
这次完成右边的操作。   
常用商品布局  

    <div class="title" >常用商品</div>
         <div class="often-goods-list">
                  <ul>
                  <li  v-for='goods in oftenGoods' @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}元</span>
             </li>
          </ul>
        </div>
     </div>
   设置相应css样式后，数据在ofenenGoods中，然后循环输出。 

商品分类布局：  
下半部分：  
	<div class="goods-type">
	 
	    <el-tabs>
	        <el-tab-pane label="汉堡">
	            汉堡
	        </el-tab-pane>
	            <el-tab-pane label="小食">
	            小食
	        </el-tab-pane>
	        <el-tab-pane label="饮料">
	            饮料
	        </el-tab-pane>
	        <el-tab-pane label="套餐">
	            套餐
	        </el-tab-pane>
	 
	    </el-tabs>
	</div>
同样加入这几个选项卡。  

     <el-tab-pane label="汉堡">
       <ul class='cookList'>
               <li v-for='goods in type0Goods' @click=" addOrderList(goods)">
               <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                <span class="foodName">{{goods.goodsName}}</span>
               <span class="foodPrice">￥{{goods.price}}元</span>
              </li>
         </ul>
       </el-tab-pane>
这样就可以使用数据type0Goods上的数据遍历输出，小食，饮料也是相同的处理。

##第八节Axios远程读取数据

	npm install axios --save
由于axios是需要打包到生产环境中的，所以我们使用–save来进行安装。  

引入Axios  
我们在Pos.vue页面引入Axios，由于使用了npm来进行安装，所以这里不需要填写路径。

	import axios from 'axios'
写到script下

	 created(){
	      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
	      .then(response=>{
	         console.log(response);
	         this.oftenGoods=response.data;
	      })
	      .catch(error=>{
	          console.log(error);
	          alert('网络错误，不能访问');
	      })
	
	
	
	       //读取分类商品列表
	      axios.get('http://jspang.com/DemoApi/typeGoods.php')
	      .then(response=>{
	         console.log(response);
	         //this.oftenGoods=response.data;
	         this.type0Goods=response.data[0];
	         this.type1Goods=response.data[1];
	         this.type2Goods=response.data[2];
	         this.type3Goods=response.data[3];
	 
	      })
	      .catch(error=>{
	          console.log(error);
	          alert('网络错误，不能访问');
	      })
	  },
写到钩子函数下，获取信息数据。

##第九节核心功能
**添加商品到订单页面** 
 
	 addOrderList(goods){
	            this.totalCount=0; //汇总数量清0
	            this.totalMoney=0;
	            let isHave=false;
	            //判断是否这个商品已经存在于订单列表
	            for (let i=0; i<this.tableData.length;i++){
	                console.log(this.tableData[i].goodsId);
	                if(this.tableData[i].goodsId==goods.goodsId){
	                    isHave=true; //存在
	                }
	            }
	            //根据isHave的值判断订单列表中是否已经有此商品
	            if(isHave){
	                //存在就进行数量添加
	                 let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
	                 arr[0].count++;
	                 //console.log(arr);
	            }else{
	                //不存在就推入数组
	                let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
	                 this.tableData.push(newGoods);
	 
	            }
	}
判断tableData表中是否已有此商品，若有就通过let arr =this.tableData.filter(o =>o.goodsId == goods.goodsId);  
筛选出tableData数组中元素的goosId ==goods.goodsId，的全部元素然后再返回到一个新的tableData中，由于Javascript中，arr引用了tableData，但是指针是相同的，则改变arr，即tableData中的数据也改变了。  

**订单列表中的增加按钮**  

	<el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
scope.row即当前行的对象。

**删除单个商品**  

      delSingleGoods(goods){
        console.log(goods);
        this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
 
      },
this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
筛选出不等的goosId，之后再返回给tableData  

**删除全部订单商品**


      //删除所有商品
        delAllGoods() {
            this.tableData = [];
        },
这样就删除全部了。  

**模拟结账**

      checkout() {
    if (this.tableData.length != 0) {
        this.tableData = [];
        this.$message({
            message: '结账成功，感谢你又为店里出了一份力!',
            type: 'success'
        });
 
    }else{
        this.$message.error('不能空结。老板了解你急切的心情！');
    }
    }
 
	
##第十节项目打包和上线

1、把绝对路径改为相对路径  

我们打开config/index.js 会看到一个build属性，这里就我们打包的基本配置了。你在这里可以修改打包的目录，打包的文件名。最重要的是一定要把绝对目录改为相对目录。 
 
	assetsPublicPath:'./'
之后再   

	npm run build