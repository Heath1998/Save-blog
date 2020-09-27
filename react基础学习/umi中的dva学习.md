## 关于react的学习umi+dva


### 关于umi

安装完umi后可以用
	
	yarn create umi

创建完命令就可以选择dva和antd来进行开发。这里主要还是讲一下dva


### dva的view层面

dva的使用包括全局的model和页面model

	//在组件的页面即views下
	import { connect } from 'dva'; // connect用于组件连接models层数据
	//....组件.....
	const TestPage = () => {
		.......
	}

	//返回自己需要的state的数据
	function mapStateToProps(state) {
	  // 这个state是所有model层的state，这里只用到其中一个，所以state.testPage把命名空间为testPage这个model层的state数据取出来
	  // es6语法解构赋值
	  const { num, shoppingStore } = state.testPage;
	  // 这里return出去的数据，会变成此组件的props，在组件可以通过props.num取到。props变化了，会重新触发render方法，界面也就更新了。
	  return {
	    num,
	    // num:num, //同上，es6语法，key/value相同名时可以简写。
	    shoppingStore,
	  };
	}

	// 最后将获得的数据通过props的方式传给TestPage组件
	//connect()返回的还是一个函数，再调用TestPage
	export default connect(mapStateToProps)(TestPage);



	//如果要调用model调用effects和reducers需要用此形式
    dispatch({
      type: 'testPage/shoppingWZ',
      who: '购物网站：', // 给shoppingWZ方法传递了who这个参数
      // 参数2:值 //多个参数可以写多个
    });


### dva的servies

就是将要请求的接口全部封装，再暴露出去，供model层调用

### dva的model层

首先model有5个属性namespace，state，reducers，effects，subscription。


	namespace: 'testPage', // 命名空间为String,且唯一。models层能有多个model，通过命名空间区分
	state: {
	num: 3, // state里能定义一些默认值，key:value形式
	shoppingStore: '',
	},




	// reducers是更新state的唯一地方，处理同步操作，把新的state retrun出去，用到state数据的界面就会更新，官方推荐处理逻辑都放在effects中
	//这样会更新state
    showShopping(state, { shoppingStore }) { // 接收action,并解构出来
      return { ...state, shoppingStore };
    },




	//主要是通过yield call调用api请求后端，获得的结果再交给reducers来更新state，要用yield put来执行reducers	

	// effects处理异步的，用于与后台交互获取数据，推荐数据逻辑处理也应该在此处理，处理完再给reducer
    *shoppingWZ({ who }, { call, put, select }) {
      //   const num = yield select((state) => state.testPage.num) //取命名空间为testPage的model的state里的num
      //   const resp = yield call(service.shoppingWZ,{name:who,age:33});//后面obj是传给后台的参数，没有可以不写
      const resp = yield call(service.shoppingWZ); // 请求后台，获取返回的数据
      const shoppingStore = who + resp.data.join(',');
      yield put({
        type: 'showShopping', // 这里触发本model的方法，可以不跟命名空间。这里触发说明reducers中的showShopping方法
        shoppingStore, // shoppingStore:shoppingStore ,es6简写，把数据传给type指定的方法
      });
      message.success('查询成功！');
    },

























