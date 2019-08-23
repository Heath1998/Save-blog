（18.12.27）
# 剑指offer（1）二维数组中的查找

从今天开始刷牛客上的剑指offer，为下学期的春招打好基础，也给下学期的蓝桥杯打好基础。一题一题来不管难易。   

本来我是用c的，但感觉c以后都很少用，所以就开始用java写了。当然语言只是跳板，重要还是看自己的算法。话不多说开始第一题：   

**时间限制：1秒 空间限制：32768K 热度指数：875361**    
**本题知识点： 查找**    

**题目描述**   
**在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数**。


这个题有很多种解法，遍历也可以。但我还是用了更友好的算法，二分查找。将每一列看成递增数组，二分查找每一个数组，代码如下。


	public class Solution {
	    public boolean Find(int target, int [][] array) {
	        int i = array.length;
	        int j = array[0].length;
	        for(int x = 0; x < i; ++x)
	        {
	            int low = 0;
	            int high = j-1;
	            while(low<=high)
	            {
	                int mid = (low + high)/2;
	                if(target > array[x][mid])
	                    low = mid + 1;
	                else if(target < array[x][mid])
	                    high = mid - 1;
	                else
	                    return true;
	            }
	        }
	        return false;
	    }
	}