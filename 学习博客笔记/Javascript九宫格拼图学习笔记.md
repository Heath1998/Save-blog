#九宫格拼图JavaScript学习笔记
---
HTML：主要是一个div id="game"游戏区，一个div id="control"控制区  
game下又有8个div小正方块  

---
CSS：每个小正方形150px，大正方形450px；

---
Javascript： 主要是移动函数move()的操作，找到要移取的方向whereCanTo(cur_div)，  
    
    var d_direct=new Array(
    [0],//为了逻辑更简单，第一个元素我们不用，我们从下标1开始使用
    [2,4],//大DIV编号为1的DIV可以去的位置，比如第一块可以去2,4号位置
    [1,3,5],
    [2,6],
    [1,5,7],
    [2,4,6,8],
    [3,5,9],
    [4,8],
    [5,7,9],
    [6,8]
     );
     //可以去的方向
    var d_posXY=new Array(
    [0],//同样，我们不使用第一个元素
    [0,0],//第一个表示left,第二个表示top，比如第一块的位置为let:0px,top:0px
    [150,0],
    [300,0],
    [0,150],
    [150,150],
    [300,150],
    [0,300],
    [150,300],
    [300,300]
    );
    //大DIV编号的位置
    var d=new Array(10);
    //保存大DIV当前装的小DIV的编号
    d[1]=1;d[2]=2;d[3]=3;d[4]=4;d[5]=5;d[6]=6;d[7]=7;d[8]=8;d[9]=0;

----
    function move(id){
    //移动函数，前面我们已将讲了
    var i=1;
    for(i=1; i<10; ++i){
        if( d[i] == id )
            break;
    }
    //这个for循环是找出小DIV在大DIV中的位置
        var target_d=0;
    //保存小DIV可以去的编号，0表示不能移动
    target_d=whereCanTo(i);
    //用来找出小DIV可以去的位置，如果返回0，表示不能移动，如果可以移动，则返回可以去的位置编号
    if( target_d != 0){
        d[i]=0;
        //把当前的大DIV编号设置为0，因为当前小DIV已经移走了，所以当前大DIV就没有装小DIV了
        d[target_d]=id;
        //把目标大DIV设置为被点击的小DIV的编号
        document.getElementById("d"+id).style.left=d_posXY[target_d][0]+"px";
        document.getElementById("d"+id).style.top=d_posXY[target_d][1]+"px";
        //最后设置被点击的小DIV的位置，把它移到目标大DIV的位置
    }
    //如果target_d不为0，则表示可以移动，且target_d就是小DIV要去的大DIV的位置编号
    var finish_flag=true;
    //设置游戏是否完成标志，true表示完成
    for(var k=1; k<9; ++k){
        if( d[k] != k){
            finish_flag=false;
            break;
            //如果大DIV保存的编号和它本身的编号不同，则表示还不是全部按照顺序排的，那么设置为false，跳出循环，后面不用再判断了，因为只要一个不符，就没完成游戏
        }
    }
    //从1开始，把每个大DIV保存的编号遍历一下，判断是否完成
    if(finish_flag==true){
        if(!pause)
            start();
        alert("congratulation");
    }
    //如果为true，则表示游戏完成，如果当前没有暂停，则调用暂停韩式，并且弹出提示框，完成游戏。
    //start()这个函数是开始，暂停一起的函数，如果暂停，调用后会开始，如果开始，则调用后会暂停
    }

-----


**这个实验没有什么特别的函数，所以主要明白数据的操作**

![图片](https://i.imgur.com/hiACvA9.png)