### 含义

文档对象模型。是针对HTML和XML文档的一个API。

### childNodes

每个节点都有一个`childNodes`属性，其中保存着一个`NodeList`对象。`NodeList`是类数组。

#### 节点的`子节点、末尾节点`

* childNodes[0] => xxxNode.firstChild

* childNodes[childNodes.length - 1] => xxxNode.lastChild

### appendChild(newNode)

向`childNodes`列表末尾添加新节点。

```
var returnNode = xxxNode.appendChild(xxxNode.firstChild)
returnNode === xxxNode.lastChild
```

### insertBefore(newNode, node)

向`childNodes`列表某个位置添加新节点。

```
// 插入到最后一个后面，成为最后一个
someNode.insertBefore(newNode, null)

// 插入到第一个前面，成为第一个
someNode.insertBefore(newNode, someNode.firstChild)

// 插入到最后一个前面，成为倒数第二个
someNode.insertBefore(newNode, someNode.lastChild);
```

### replaceChild(newNode, node)

替换 `node => newNode`

### removeChild(node)

删除`node`节点。

### cloneNode(true | false)

复制节点。接受1个参数，是否深度复制