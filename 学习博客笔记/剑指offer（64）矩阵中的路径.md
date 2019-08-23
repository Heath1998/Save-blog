（19.3.11）
# 剑指offer（64）矩阵中的路径
 
**题目描述    
请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则之后不能再次进入这个格子。 例如 a b c e s f c s a d e e 这样的3 X 4 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。**  


思路：其实就是回溯法，设数组arr为对应的字符串路径，flag为是否走过，走过标记1，path为剩下要走的路径。   
check函数是检查当前是否可走。   
判断path是否为空，是就已走完。返回true   
判断当前位置是否可走若不可走return false    
之后置flag当前x，y位置为1.    
最后返回四种走的路线相或一种可走即可。



代码如下：


	function hasPath(matrix, rows, cols, path)
	{
	    var arr = [];
	    var flag = initalflag(rows,cols);
	    for(var x=0;x<rows;x++)
	    arr.push([]);
	    for(var i=0;i<rows;i++)
	        for(var j=0;j<cols;j++)
	            arr[i][j] = matrix[i*cols+j];   
	    for(var i=0;i<rows;i++)
	        for(var j=0;j<cols;j++)
	        {
	            if(check(arr,flag,i,j,path))
	                return true;
	            flag = initalflag(rows,cols);
	        }
	    return false;
	}
	
	function initalflag(rows,cols){
	    var flag = [];
	    for(var x=0;x<rows;x++)
	    flag.push([]);
	    for(var i=0;i<rows;i++)
	    for(var j=0;j<cols;j++)
	        flag[i][j] = 0;
	    return flag;
	}
	
	function check(arr,flag,x,y,path)
	{
	    if(path==""){
	        return true;
	    }
	    if(x<0||x>=flag.length||y<0||y>=flag[0].length||arr[x][y]!=path[0]||flag[x][y])
	        return false;
	    flag[x][y] = 1;
	    return check(arr,flag,x,y-1,path.slice(1))||
	    check(arr,flag,x+1,y,path.slice(1))||
	    check(arr,flag,x,y+1,path.slice(1))||
	    check(arr,flag,x-1,y,path.slice(1));
	}
	
	console.log(hasPath('abcesfcsadee',3,4,'abcd'));