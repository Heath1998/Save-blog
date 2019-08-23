# Vue2.0用TypeScript+vuex重构项目

不得不说现在前端的技术更新越来越快了，一不学习就要给这个大环境淘汰了。

Typescript是javascript的超集。本质上向这个语言添加了可选的静态类型和基于类的面向对象编程。其中我认为最重要的就是静态类型检查了。

写程序时项目若没有良好的设计思想，用js写的话就会“重构火葬场”。

话不多说，直接干货：  

## 写在前面

 Vue我已经学习了一个学期了，所以现在刚接触TypeScript，Typescript完全空白，项目迁移至Typescript，因此这篇文章更像个入门指引。

## 依赖引入介绍

一般vue2.0改成TypeScript要引入以下依赖

最基础的ts-loader和typescript依赖这是typescript必须的

    "ts-loader": "^3.3.1",
    "typescript": "^3.2.2",
	//这里版本一定要低，不然需要webpack4.0以上了。

之后是装饰vue-property-decorator：非官方维护，一定学习成本

    "vue-property-decorator": "^7.3.0",

之后是vuex-class：非官方维护，在 vue-class-component 基础上补充一定vuex支持（支持有限）

    "vuex-class": "^0.3.1"

##  Webpack配置改变

以vue-cli为例子

resolve.extensions添加.ts  

	resolve: {
	  extensions: ['.js', '.ts', '.vue', '.json']
	}

&#9733; module.rules添加.ts解析规则：

	module: {
	  rules: [
	    {
	      test: /\.tsx?$/,
	      loader: 'ts-loader',
	      exclude: /node_modules/,
	      options: {
	        appendTsSuffixTo: [/\.vue$/]
	      }
	    }
	  ]
	}

## tsconfig.json

添加在项目根目录下即可。

	// tsconfig.json
	{
	  "compilerOptions": {
	    // 与 Vue 的浏览器支持保持一致
	    "target": "es5",
	    // 这可以对 `this` 上的数据属性进行更严格的推断
	    "strict": true,
	    // 如果使用 webpack 2+ 或 rollup，可以利用 tree-shake:
	    "module": "es2015",
	    "moduleResolution": "node"
	  }
	}

## vue-shim.d.ts

src目录下添加文件vue-shim.d.ts：

	declare module "*.vue" {
	  import Vue from "vue";
	  export default Vue;
	}

意思是告诉TypeScript *.vue后缀的文件可以交给vue模块来处理。

## .js 文件重命名为 .ts 文件

之后就是把文件中的import引入的js改为ts。  
注意：重命名后对vue文件的import，需添加.vue后缀

## .vue 文件改造


	<script>标签添加lang="ts"声明

之后是使用vue-property-decorator进行代码重构。

写个简单例子。

    export default {
    name:'Home',
    data() { //选项 / 数据
        return {
			time: "now"
        }
    },
    methods: { //事件处理器
	add() {
	return 12;
	}
    },
    components: { //定义组件
        'wbc-nav':header,
    },
    created() { //生命周期函数

    }
	}

等于如下例子

	import {Vue,Component} from "vue-property-decorator"
	@Component({
    components: { //定义组件
    'wbc-nav':header
    }
	})
	export default class Home extends Vue{
	time: string = 'now';
	add(): number{
	}
	created() {
	}
	}

当然实际不可能这么简单还有@Prop等的使用方法，去网上找吧。

## 是使用vuex-class


实话实说typescript与vuex的适配基本上都很差，所以也只能半推半就。以我一个学生的水平，我里面有些变量实在不懂，只好用any了。不多说了上代码

	import {Vue,Component} from "vue-property-decorator"
	import {  State,  Getter,  Action,  Mutation,  namespace } from 'vuex-class'
	@Component({
	    components: { //定义组件

	    }
	})
	export default class Home extends Vue {
	    //State
	    @State author!: string;
	    //Getter
	    @Getter name!: string;//作者
	    //Mutation
	    @Mutation SET_AUTHOR: any;
	    //Action
	    @Action SET_AUTHOR_ASYN: any;   
	    mounted(){
	        console.log(this.author);
	        console.log(this.name);
	        this.SET_AUTHOR("帅锅");
	        console.log(this.author);
	        this.SET_AUTHOR_ASYN("这个是异步");
	        setTimeout(()=>{ console.log(this.author);}, 5000);
	    }
	}

有些地方要说一下 @State author!: string;为什么有感叹号呢？你就当做它要断言初始化一下。

## 最后

我也发现了代码重构的好处就是，下次改代码不用大幅度改变，这也算个优点吧。好了放寒假了，还是要加把劲阿，春招快了。