（19.1.14）
# 剑指offer（19）顺时针打印矩阵

**题目描述**  
**输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.**


这种题思想很简单，但操作起来其实不那么容易。

就是计算出它的循环圈数，也就是较小的那个列数或行数除以2向上取整。

再在圈中的循环中用四个for操作它从左到右，上到下，右到左，下到上。

四个变量up，down，left，right。右到左，和下到上要判断down大于up，left小于right


代码如下：


	var arr = [[1,2],[3,4],[5,6]];
	
	function printMatrix(matrix)
	{
	    // write code here
	    if(matrix==null || matrix.length==0) return;
	    var res = [];
	    var row = matrix[0].length;
	    var col = matrix.length;
	    var circle = Math.ceil((row > col ? col : row) / 2);
	    var left = 0;
	    var right = row - 1;
	    var up = 0;
	    var down = col - 1;
	    for(var k = 0; k < circle; ++k) 
	    {
	        for(var i = left; i <= right; ++i)
	            res.push(matrix[up][i]);
	        ++up;
	        for(var j = up; j <= down; ++j)
	            res.push(matrix[j][right]);
	        --right;
	        if(down > up -1)
	        {
	            for(var m = right; m >= left; --m)
	            res.push(matrix[down][m]);
	        }
	        --down;
	        if(left < right + 1) 
	        {
	            for(var n = down; n >= up; --n)
	            res.push(matrix[n][left]);
	        }
	        ++left;
	    }
	    return res;
	}
	
	console.log(printMatrix(arr));