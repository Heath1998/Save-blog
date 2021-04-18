#   Vue的patchChildren的具体步骤



##   patchKeyChildren



```javascript
  const patchKeyedChildren = (
    c1: VNode[],
    c2: VNodeArrayChildren,
    container: RendererElement,
    parentAnchor: RendererNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: SuspenseBoundary | null,
    isSVG: boolean,
    optimized: boolean
  ) => {
    let i = 0
    const l2 = c2.length
    let e1 = c1.length - 1 // prev ending index
    let e2 = l2 - 1 // next ending index
   ....... 
   }
```

主要设置出事值获取c2长度，e1，e2分别为c1，c2结尾下标。



###    从前到后 from start

```javascript
    // 1. sync from start
    // (a b) c
    // (a b) d e
    while (i <= e1 && i <= e2) {
      const n1 = c1[i]
      const n2 = (c2[i] = optimized
        ? cloneIfMounted(c2[i] as VNode)
        : normalizeVNode(c2[i]))
      if (isSameVNodeType(n1, n2)) {
        patch(
          n1,
          n2,
          container,
          null,
          parentComponent,
          parentSuspense,
          isSVG,
          optimized
        )
      } else {
        break
      }
      i++
    }
```

从前到后遍历，直到c1或c2结尾，或者不相等`c1[i]!==c2[i]`即退出。判断isSameVnodeType是不是一个类型同样的key，是的话执行patch进行更新。否则break退出。i为第一个不同的位置



###  从后到前 from end

```javascript
    // 2. sync from end
    // a (b c)
    // d e (b c)
    while (i <= e1 && i <= e2) {
      const n1 = c1[e1]
      const n2 = (c2[e2] = optimized
        ? cloneIfMounted(c2[e2] as VNode)
        : normalizeVNode(c2[e2]))
      if (isSameVNodeType(n1, n2)) {
        patch(
          n1,
          n2,
          container,
          null,
          parentComponent,
          parentSuspense,
          isSVG,
          optimized
        )
      } else {
        break
      }
      e1--
      e2--
    }
```

从后往前遍历，当isSameVNodeType为同一个类型和key，就执行patch进行更新。更新完e1--，e2--，e1，e2，都从末尾下标开始。 不是同个key就直接break。



###    添加新节点

```javascript
    // 3. common sequence + mount
    // (a b)
    // (a b) c
    // i = 2, e1 = 1, e2 = 2
    // (a b)
    // c (a b)
    // i = 0, e1 = -1, e2 = 0
    if (i > e1) {
      if (i <= e2) {
        const nextPos = e2 + 1
        const anchor = nextPos < l2 ? (c2[nextPos] as VNode).el : parentAnchor
        while (i <= e2) {
          patch(
            null,
            (c2[i] = optimized
              ? cloneIfMounted(c2[i] as VNode)
              : normalizeVNode(c2[i])),
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG
          )
          i++
        }
      }
    }
```

`if` 如果当i > e1说明新的c2增加了新节点，遍历从i到e2通过patch(null,c[2])的形式来添加dom真实元素。传入锚点anchor为当前e2+1的位置，如果刚好不存在会等于父级中的子结尾的对象，默认没有东西的，只是一个子结尾，方便插入。



###   删除多余节点

```javascript
    // 4. common sequence + unmount
    // (a b) c
    // (a b)
    // i = 2, e1 = 2, e2 = 1
    // a (b c)
    // (b c)
    // i = 0, e1 = 0, e2 = -1
    else if (i > e2) {
      while (i <= e1) {
        unmount(c1[i], parentComponent, parentSuspense, true)
        i++
      }
    }
```

`else if`判断当前i>e2,并且删除当前c1[i]对应的节点，遍历从i到e1结束，执行unmount。



###   进行元素节点的移动复用和删除和增加

`else `, 在这个地方是`else `结果，主要进行中间序列元素的复用，增加和删除。



####   设置keyToNewIndexMap    

```javascript
      const s1 = i // prev starting index
      const s2 = i // next starting index

      // 5.1 build key:index map for newChildren
      const keyToNewIndexMap: Map<string | number, number> = new Map()
      for (i = s2; i <= e2; i++) {
        const nextChild = (c2[i] = optimized
          ? cloneIfMounted(c2[i] as VNode)
          : normalizeVNode(c2[i]))
        if (nextChild.key != null) {
          if (__DEV__ && keyToNewIndexMap.has(nextChild.key)) {
            warn(
              `Duplicate keys found during update:`,
              JSON.stringify(nextChild.key),
              `Make sure keys are unique.`
            )
          }
          keyToNewIndexMap.set(nextChild.key, i)
        }
      }
```

主要通过遍历c2来设置`keyToNewIndexMap`当前剩余c2的key值，和当前的下标i为value。





####   设置newIndexToOldIndexMap这个数组

```javascript
      // 5.2 loop through old children left to be patched and try to patch
      // matching nodes & remove nodes that are no longer present
      let j
      let patched = 0
      const toBePatched = e2 - s2 + 1
      let moved = false
      // used to track whether any node has moved
      let maxNewIndexSoFar = 0
      // works as Map<newIndex, oldIndex>
      // Note that oldIndex is offset by +1
      // and oldIndex = 0 is a special value indicating the new node has
      // no corresponding old node.
      // used for determining longest stable subsequence
      const newIndexToOldIndexMap = new Array(toBePatched)
      for (i = 0; i < toBePatched; i++) newIndexToOldIndexMap[i] = 0

      for (i = s1; i <= e1; i++) {
        const prevChild = c1[i]
        if (patched >= toBePatched) {
          // all new children have been patched so this can only be a removal
          unmount(prevChild, parentComponent, parentSuspense, true)
          continue
        }
        let newIndex
        if (prevChild.key != null) {
          newIndex = keyToNewIndexMap.get(prevChild.key)
        } else {
          // key-less node, try to locate a key-less node of the same type
          for (j = s2; j <= e2; j++) {
            if (
              newIndexToOldIndexMap[j - s2] === 0 &&
              isSameVNodeType(prevChild, c2[j] as VNode)
            ) {
              newIndex = j
              break
            }
          }
        }
        if (newIndex === undefined) {
          unmount(prevChild, parentComponent, parentSuspense, true)
        } else {
          newIndexToOldIndexMap[newIndex - s2] = i + 1
          if (newIndex >= maxNewIndexSoFar) {
            maxNewIndexSoFar = newIndex
          } else {
            moved = true
          }
          patch(
            prevChild,
            c2[newIndex] as VNode,
            container,
            null,
            parentComponent,
            parentSuspense,
            isSVG,
            optimized
          )
          patched++
        }
      }
```

这个newIndexTooldIndexMap是字面意思，新child的index下标对应着老的child的index，如果不存在就为0.     

主要逻辑先设置`newIndexToOldIndexMap`生成剩余c2的长度的数组，并全部赋值为0。     



之后遍历剩余c1，判断patched是否大于等于toBepatched，大于就unmount删除当前真是dom节点。patched是已经找到oldchild对应newchild的个数。`toBePatched`是当前剩余c2的个数。

判断当前的prechild是否有在c2中有对应的key，通过`keyToNewIndexMap`来确认是否存在。       

存在： 

* 存在就设置当前`newIndexToOldIndexMap`c2对应下标的值为当前prechild+1。这里+1是因为一开始设置了全部`newIndexToOldIndexMap`为0，怕混淆，不知道是否c2有与c1对应。判断是否move         

* 再执行patch(prevChild,c2[newIndex])执行更新，这里newIndex是当前prevChild对应key在`keyToNewIndexMap`找到的。

* 执行patched++，就是匹配个数加1

不存在：

* newIndex不存在，执行删除操作。



####   move移动和mount创建

```javascript
      // 5.3 move and mount
      // generate longest stable subsequence only when nodes have moved
      const increasingNewIndexSequence = moved
        ? getSequence(newIndexToOldIndexMap)
        : EMPTY_ARR
      j = increasingNewIndexSequence.length - 1
      // looping backwards so that we can use last patched node as anchor
      for (i = toBePatched - 1; i >= 0; i--) {
        const nextIndex = s2 + i
        const nextChild = c2[nextIndex] as VNode
        const anchor =
          nextIndex + 1 < l2 ? (c2[nextIndex + 1] as VNode).el : parentAnchor
        if (newIndexToOldIndexMap[i] === 0) {
          // mount new
          patch(
            null,
            nextChild,
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG
          )
        } else if (moved) {
          // move if:
          // There is no stable subsequence (e.g. a reverse)
          // OR current node is not among the stable sequence
          if (j < 0 || i !== increasingNewIndexSequence[j]) {
            move(nextChild, container, anchor, MoveType.REORDER)
          } else {
            j--
          }
        }
      }
```



首先`getSequence(newIndexToOldIndexMap)`获取`increasingNewIndexSequence`最长增长序列。

`j = increasingNewIndexSequence.length - 1`。      

遍历newIndexToOldIndexMap数组，从后往前遍历。判断当前newIndexToOldIndexMap对应值是否为0.        

如果`if`为`newIndexToOldIndexMap[i] === 0`执行patch(null, nextChild)进行创建新节点，传入anchor锚插入点。         

`else if (moved)`如果移动为`true`，判断当前`increasingNewIndexSequence`最长增长序列的j下标的值，是否与当前i相等，即`newIndexToOldIndexMap`遍历到的位置。如果j<0时或者不相等时，执行move移动函数，否则就不需要移动直接j--，继续for。



move是获取后面newChildren的下一个的位置插入inserBefore的。