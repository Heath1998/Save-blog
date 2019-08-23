(19.1.1)
# 剑指offer（5）用两个栈实现队列

新年到了，算了，还是好好学习吧，明天要考试了。   

两个栈假设有stack1和stack2   

其实就是第一个做入栈，第二个把第一个逆置入栈后做出栈。    

核心思想： 当队列入队列时stack1.push(node)进行入，当队列要出队列时，先检查stack2是否为空，     
如果是就先将stack1的全部pop出栈再入stack2入栈，这样stack2就是stack1的倒序了，之后return stack2.pop();     
否则就将stack2直接 return    stack2.pop();  

算了还是直接贴代码容易懂，我感觉我也讲不清：   


	var stack1 = [];
	var stack2 = [];
	
	
	function push(node) {
	    stack1.push(node);
	}
	
	function pop() {
	    if(stack2.length === 0) {
	        while(stack1.length !== 0) {
	            stack2.push(stack1.pop());
	        }
	    }
	    return stack2.pop();
	}
	
	push(1);
	push(2);
	push(3);
	push(4);
	console.log(pop());