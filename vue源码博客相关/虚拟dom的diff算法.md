# 虚拟dom的patch函数过程

当数据变动，
会调用render字符串生成新的虚拟dom对象。

之后执行patch(oldvnode, newvnode, vm)这个函数进行比较

会执行sameVnode（）比较两个虚拟dom节点，比较两者key值，tag标签还有data， isComment(是否是注释节点)

相同的话执行patchVnode

首先判断新的vnode是不是文本节点，如果不是再判断他的子节点


patchVnode有以下几种情况
1.判断新的vnode的text文本是否存在，是的话比较一下新旧vnode的text，并且更新实际dom的文本

2.当新的vnode的text文本不存在，即不为undefined。又有以下几种比较

2.1当新旧的子节点都存在，且不相等调用updataChildern更新

2.2当新的vnode存在，就在实际dom上添加

2.3当旧的vnode的孩子存在，就删除实际dom上的节点，

2.4当旧的vnode的text存在，就给实际dom的text文本置空


updateChildern()

主要五种比较方式：按顺序
最后一种用key值比较

第一种   
oldChildern的oldStart即第一个节点与newChildern的newStart比较sameVnode，值得比较就会调用patchVnode方法比较，若相等则oldStartVnode和newStartVnode都会向后移动一位。

第二种    
oldChildern的oldEndVnode即倒数第一个位置与newChildern的newEndVnode比较sameVnode，值得比较就会调用patchVnode方法比较，并且则oldEndVnode和newEndVnode都会向前移动一位

第三种    
oldChildern的第一个节点oldStartVnode和newChildern的最后一个节点newEndVnode比较sameVnode，值得比较就会调用patchVnode方法比较，
并且在oldStartVnode.elm向右移动到oldEndvnode.elm的后边，同时oldStartVnode向后移动一位，newEndVnode向前移动一位。

第四种    
oldChildern的最后一个节点oldEndVnode和newChildern的第一个节点oldStartVnode比较sameVnode，值得比较就会调用patchVnode方法比较，值得比较就会调用patchVnode方法比较, 并且oldEndVnode添加到oldStartVnode的前面。同时oldEndVnode向前移动一位，newStartVnode向后移动一位。

第五种   
使用key值比较。   
会有一个key-index表，先判断newStart的key值是否存在vnode.key中。  

如果不存在，此时是一个新的vnode
将这个vnode插入到oldStartVnode.elm的前边，把newStartVnode设置为下一个节点，

如果存在，sameVnode比较vnode.key找出的和newStartvnode比较，如果值得比较
pacthVnode两者，同时把oldChildern[index].elm的位置移到oldStartVnode.elm的前面，oldChildern[index]位置设为undefined，同时newStartVnode向后移一位。   

如果不值得比较key值又相同，可能tag不同，此时创建一个新节点crateElm并把
newStartVnode向后移一位。