（19.2.11）

# 剑指offer（27）字符串的排序

重新开始做题感觉有点难度，但还是要坚持的。

**题目描述**  
**输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。**   
**输入描述:   
输入一个字符串,长度不超过9(可能有字符重复),字符只包括大小写字母。**

这题思路就是全排序，不过要用字典序这个排序前排好，还有去掉重复的，可以设置一个obj空对象，没有就允许。

思路：

![](https://uploadfiles.nowcoder.com/images/20170705/7578108_1499250116235_8F032F665EBB2978C26C4051D5B89E90)

就是先固定第一个，递归剩下的

这个题还是要看代码才会写的。

js代码如下：

	var result = [];
	function Permutation(str){
	    result = []
	    if(str.length<=0) return result;
	    var sortTmp = "";
	    var arr = str.split("");
	    result = sortString(arr,sortTmp)
	    return result;
	}
	  
	function sortString(arr,sortTmp){
	    if(arr.length == 0) {
	        result.push(sortTmp);
	    }
	    else {
	        var obj = {};
	        for(var i = 0; i<arr.length; i++)
	        {
	            if(!obj[arr[i]]) {
	                var tmp = arr.splice(i,1)[0];
	                sortTmp = sortTmp + tmp;
	                sortString(arr, sortTmp);
	                arr.splice(i,0,tmp);
	                sortTmp = sortTmp.slice(0, sortTmp.length-1);
	                obj[tmp] = true;
	            }
	        }
	    }
	    return result;
	}
	var arr = "123";
	console.log(Permutation(arr));


可以这样想sortString递归srr.length次，for是可以选放到当前位置的个数。
