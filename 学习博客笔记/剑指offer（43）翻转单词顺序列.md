（19.2.26）

# 剑指offer（43）翻转单词顺序列


最近几天找实习，面经看得好慌。


**题目描述
牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？**


思路：遇到空格就分开分成一个数组在倒序，之后就是相加了。

javascript挺好写的。


代码如下：


	function ReverseSentence(str)
	{
	    var arr = str.split(' ');
	    arr.reverse();
	    var result = "";
	    for(var i=0;i<arr.length;i++)
	        result=result+ (arr.length-1==i ? arr[i]:arr[i]+" ");
	    return result;
	}
	
	var arr = "123456 21 2";
	
	console.log(ReverseSentence(arr));