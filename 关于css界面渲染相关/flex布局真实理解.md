#  flex布局真实理解

[网址](https://www.cnblogs.com/echolun/p/11299460.html)

##  父元素可选样式

###  **1.flex-direction属性**

设置主轴方向，取值：`row(默认) | row-reverse | column | column-reverse`

![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190804214423463-1446742040.png)

###  2.flex-wrap属性

取值：nowrap(默认) | wrap | wrap-reverse

用于控制项目是否换行，nowrap表示不换行

![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190806225229283-1107311503.png)



###  3.**justify-content属性**  

取值：flex-start(默认) | flex-end | center | space-between | space-around | space-evenly;      

用来控制在主轴的排序，如果flex-direction是row的话，就是横轴。   

![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190805182705299-5638313.png)



###  4.**align-items属性**

取值：flex-start | flex-end | center | baseline | stretch(默认)

用于控制项目在纵轴排列方式，默认stretch即如果项目没设置高度，或高度为auto，则占满整个容器，下面第一张图的项目没设置高度，其余图片中均为60px。

![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190805232037403-422790942.png)

center使用最多，自然不会陌生，在纵轴中心位置排列：

![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190805233530662-1567383076.png)





##   子元素可设置属性



###  **1.order**

取值：默认0，用于决定项目排列顺序，数值越小，项目排列越靠前。



###  **flex-grow**

取值：默认0，用于决定项目在有剩余空间的情况下是否放大，默认不放大；注意，即便设置了固定宽度，也会放大。即按比例来。          

优先级比width高

![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190808185733909-1052417826.png)





###   **3.flex-shrink**

取值：默认1，用于决定项目在空间不足时是否缩小，默认项目都是1，即空间不足时大家一起等比缩小；注意，即便设置了固定宽度，也会缩小。

但如果某个项目flex-shrink设置为0，则即便空间不够，自身也不缩小。



![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190808191100715-1387948858.gif)



###   **4.flex-basis**



取值：默认auto，用于设置项目宽度，默认auto时，项目会保持默认宽度，或者以width为自身的宽度，但如果设置了flex-basis，权重会width属性高，因此会覆盖widtn属性。



![](https://img2018.cnblogs.com/blog/1213309/201908/1213309-20190808192004493-824002338.png)



###   5**flex**

取值：默认0 1 auto，flex属性是flex-grow，flex-shrink与flex-basis三个属性的简写，用于定义项目放大，缩小与宽度。

该属性有两个快捷键值，分别是auto(1 1 auto)等分放大缩小，与none(0 0 auto)不放大不缩小。