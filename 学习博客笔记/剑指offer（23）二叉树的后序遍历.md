（19.1.22）
# 剑指offer（23）二叉树的后序遍历

**输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。**

二叉搜索树满足如下例子

![![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/da/Binary_search_tree.svg/300px-Binary_search_tree.svg.png)](https://upload.wikimedia.org/wikipedia/commons/thumb/d/da/Binary_search_tree.svg/300px-Binary_search_tree.svg.png)

1，取当前数组最后一位即为sequence[end].  
2,--i向前遍历直到找到一个位置设为j比sequeence[end]小的数
3，从j开始遍历到start看是否有比sequeence[end]大的数，有返回false
4，return 左子树和右子树进行递归。

代码如下：


	function adjust(sequence, start, end) {
	    if(start >= end) {
	        return true;
	    }
	    let i = end;
	    while(i > start && sequence[i-1] > sequence[end]) {
	        --i;
	    }
	    for(let j = i - 1; j >=start; --j) {
	        if(sequence[j] > sequence[end])
	            return false;
	    }
	    return adjust(sequence, start, i - 1) && adjust(sequence, i, end - 1)
	}
	
	function VerifySquenceOfBST(sequence)
	{
	    // write code here
	    if(sequence.length == 0 )
	        return false;
	    return adjust(sequence, 0 ,sequence.length - 1)
	}