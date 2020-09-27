# react学习回想

### 基础代码
	// class的形式
	class Welcome extends React.Component {
	  render() {
	    return <h1>Hello, {this.props.name}</h1>;
	  }
	}
	
	// 函数的形式
	function Welcome(props) {
	  return <h1>Hello, {props.name}</h1>;
	}

	// 两种形式都可以，不过普遍用class声明

### State的使用

	class Clock extends React.Component {
	  constructor(props) {
	    super(props);
	    this.state = {date: new Date()};
	  }

	// State的更改只能用setState
	  tick() {
	    this.setState({
	      date: new Date()
	    });
	  }
	
	  render() {
	    return (
	      <div>
	        <h1>Hello, world!</h1>
	        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
	      </div>
	    );
	  }
	}
	


## 关于事件处理

	// 可以在render中使用bind绑定this因为class的方法默认不会绑定this，bind后面是其他参数
    return (
      <button onClick={this.handleClick.bind(this, ....)}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );


## 列表和key

	// key只用在map即可
	function NumberList(props) {
	  const numbers = props.numbers;
	  const listItems = numbers.map((number) =>
	    <li key={number.toString()}>
	      {number}
	    </li>
	  );
	  return (
	    <ul>{listItems}</ul>
	  );
	}

## 状态提升

其实就是，子组件执行父组件的方法，从而改变数据，使新数据传向另一个子组件。




## 组合

两种方法一种是利用props传递组件，另一种是利用props.childer

	// 将组件声明为变量传值进入使用

	function SplitPane(props) {
	  return (
	    <div className="SplitPane">
	      <div className="SplitPane-left">
	        {props.left}
	      </div>
	      <div className="SplitPane-right">
	        {props.right}
	      </div>
	    </div>
	  );
	}
	
	function App() {
	  return (
	    <SplitPane
	      left={
	        <Contacts />
	      }
	      right={
	        <Chat />
	      } />
	  );
	}



	// 直接使用组件中间的，这个就叫props.childern
	function FancyBorder(props) {
	  return (
	    <div className={'FancyBorder FancyBorder-' + props.color}>
	      {props.children}
	    </div>
	  );
	}
	
	function WelcomeDialog() {
	  return (
	    <FancyBorder color="blue">
	      <h1 className="Dialog-title">
	        Welcome
	      </h1>
	      <p className="Dialog-message">
	        Thank you for visiting our spacecraft!
	      </p>
	    </FancyBorder>
	  );
	}
