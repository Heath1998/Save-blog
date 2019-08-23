(19.3.11)
# 剑指offer（60）序列化二叉树

**请实现两个函数，分别用来序列化和反序列化二叉树**

序列化就是一种遍历方式，如果遇到空节点就加入‘#’号

代码如下：

	function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	var arr = [];
	function Serialize(pRoot)
	{
	    if(!pRoot) arr.push('#');
	    else{
	        arr.push(pRoot.val);
	        Serialize(pRoot.left);
	        Serialize(pRoot.right);
	    }
	}
	function Deserialize(s)
	{
	    var node = null;
	    var val = arr.shift();
	    if(typeof val == 'number') {
	        node = new TreeNode(val);
	        node.left = Deserialize(arr);
	        node.right = Deserialize(arr);
	    }
	    return node;
	}