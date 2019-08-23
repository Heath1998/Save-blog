（19.3.11）
# 剑指offer（65）机器人的运动范围

**题目描述   
地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？**

思路：其实就是回溯法和走迷宫差不多

代码如下：




	function movingCount(threshold, rows, cols)
	{
	    var count=0;
	    var flag = [];
	    for(var j=0;j<rows;j++)
	        flag.push([]);
	    for(var i=0;i<rows;++i)
	        for(var j = 0;j<cols;++j)
	            flag[i][j] = 0;
	        
	    check(0,0,flag,threshold);
	    return count;
	    function check(x,y,flag,threshold)
	{
	    if(x<0||y<0||x>=flag.length||y>=flag[0].length||flag[x][y]==1||(countNum(x)+countNum(y)>threshold))
	        return false;
	    flag[x][y]=1
	    count++;
	    check(x-1,y,flag,threshold);
	    check(x,y+1,flag,threshold);
	    check(x+1,y,flag,threshold);
	    check(x,y-1,flag,threshold);
	}
	
	function countNum(num){
	    var arr = num.toString().split("");
	    if(arr.length == 1) {
	        return num;
	    }
	    var sum = 0;
	    for(var i = 0; i < arr.length; i++) {
	        sum += Number(arr[i]);
	    }
	    return sum;
	}
	
	}
	
	
	console.log(movingCount(15,20,20))