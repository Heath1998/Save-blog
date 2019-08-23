（19.2.13）
# 剑指offer（29）最小的k个数

**题目描述   
输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。**

这题涉及性能优化，我能写出的最好算法就是快排了，快排还是要写代码时还不熟悉，所以要多写记住它。


	function GetLeastNumbers_Solution(input, k)
	{
	     var len = input.length;
	    if(len < k) return [];
	    // write code here
	    quicksort(input,0,input.length-1);
	    
	    return input.slice(0,k);
	}
	
	function quicksort(arr, low, high) {
	    if(low < high) {
	        var p =parition(arr,low,high);
	        quicksort(arr,low,p-1);
	        quicksort(arr,p+1,high);
	    }
	}
	
	function parition(arr,left,right) {
	    var pivot = arr[left];
	    var p = left;
	    for(var i=left+1;i<=right;i++) {
	        if(pivot>arr[i]) {
	            p++;
	            if(p!=i) {
	                var t = arr[p];
	                arr[p] = arr[i];
	                arr[i] = t;
	            }
	        }
	    }
	    arr[left] = arr[p];
	    arr[p] = pivot;
	    return p;
	}