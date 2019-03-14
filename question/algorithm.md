<a id="summary"></a>
1、基本排序的方式

冒泡、快排、桶排、堆排序、归并排序、插入排序。

基本也就这些吧。最重要的还是快排和插入排序和堆排序。自己多写几次，有递归形式的再好好看一下使用循环形式达到结果的形式，基本也就没什么问题了。

2、二分搜索等。

3、二叉树遍历相关。

例如前序遍历中序遍历后序遍历等，深度优先搜索和广度优先最好掌握。还有二叉树的反转。

算法面试总结：

最重要的还是快排和插入排序和堆排序，有递归形式的再好好看。如果你算法不好就可以直接告诉面试官。不需要过多作假，面试官其实也很聪明的，如果你作弊很快就能发现的。

<a id="BubbleSort"></a>
### 冒泡排序

#### 初级

```
function bubbleSort(arr) {
  if (arr.length <= 1) {
    return arr
  }

  var len = arr.length

  for (var i = 0; i <= len - 1; i++) {
    for (var j = 0; j <= len -1; j++) {
      if (arr[j] > arr[j + 1]) {
        // var temp
        // temp = arr[j]
        // arr[j] = arr[j + 1]
        // arr[j + 1] = temp
        // 不通过临时变量实现变量交换
        arr[j + 1] = [arr[j], arr[j] = arr[j + 1]][0]
      }
    }
  }

  return arr
}
```

#### 改进

```
function bubbleSort(arr) {
  if (arr.length <= 1) {
    return arr
  }

  var i = arr.length -1
  while (i > 0) {
    var pos = 0;
    for (var j = 0; j < i; j++) {
      if (arr[j] > arr[j + 1]) {
        pos = j;
        arr[j + 1] = [arr[j], arr[j] = arr[j + 1]][0]
      }
    }
    i = pos
  }

  return arr
}
```

#### 加强

```
function bubbleSort(arr) {
  if (arr.length <= 1) {
    return arr
  }

  var low = 0;
  var high = arr.length -1;

  while(low < high) {
    for (var j = low; j < high; j++) {
      if (arr[j] > arr[j + 1]) {
        arr[j + 1] = [arr[j], arr[j] = arr[j + 1]][0]
      }
    }
    --high
  }
}
```

<a id="QuickSort"></a>
### 快速排序

```
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr
  }
  
  // 计算参照位
  var cIndex = Math.floor(arr.length / 2)

  var c = arr.splice(cIndex, 1)
  // 注意 Number([1]) === 1
  var l = []
  var r = []

  for (var i = 0; i < arr.length; i++) {
    if (arr[i] < c) {
      l.push(arr[i])
    } else {
      r.push(arr[i])
    }
  }

  return quickSort(l).concat(c, quickSort(r))
}
```

<a id="SelectionSort"></a>
### 选择排序

```
function selectionSort (arr) {
  if (arr.length <= 1) {
    return arr
  }

  for (var i = 0; i < arr.length - 1; i++) {
    var minIndex = i;
    for (var j = i + 1; j < arr.length; j++) {
      if (arr[minIndex] > arr[j]) {
        minIndex = j;
      }
    }
    arr[i] = [arr[minIndex], arr[minIndex] = arr[i]][0]
  }

  return arr
}
```

<a id="InsertionSort"></a>
### 插入排序

#### 初级

```
function insertionSort (arr) {
  if (arr.length <= 1) {
    return arr
  }
}
```

<a id="BucketSort"></a>
### 桶排序

```
function bucketSort (arr) {
  if (arr.length <= 1) {
    return arr
  }

  var i;
  var minValue = arr[0];
  var maxValue = arr[0];
}

```

<a id="BinarySearch"></a>
### 二分搜索法

也称折半搜索，是一种在有序数组中查找特定元素的搜索算法。

实现步骤：

　　1. 首先从数组中间开始查找对比，若相等则找到，直接返回中间元素的索引。

　　2. 若查找值小于中间值，则在小于中间值的那一部分执行步骤1的操作。

　　3. 若查找值大于中间值，则在大于中间值的那一部分执行步骤1的操作。

　　4. 否则，返回结果为查不到，返回-1。

```
// 递归实现
function binarySeach(target, arr, start, end) {
  var start = start || 0
  var end = end || arr.length - 1
  var mid = parseInt(start + (end - start) / 2)

  // 排除查找对象在数组之外
  if (target > arr[end] || target < arr[start]) {
    return -1
  } else if (target === arr[mid]) {
    return true
  } else if (target > arr[mid]) {
    return binarySeach(target, arr, mid + 1, end)
  } else {
    return binarySeach(target, arr, start, mid - 1)
  }
}

// 不适用递归
function binarySeach(target, arr, start, end) {
  var start = start || 0
  var end = end || arr.length - 1
  
  // 排除查找对象在数组之外
  if (target > arr[end] || target < arr[start]) {
    return -1
  }

  while (start <= end) {
    var mid = parseInt(start + (end - start) / 2)

    if (target === arr[mid]) {
      return mid
    } else if (target > arr[mid]) {
      start = mid + 1
    } else {
      end = mid - 1
    }
  }
}
```

#### 无序的二分搜索

```
function binarySeach(target, arr) {
  while (arr.length > 0) {
    // 快速排序
    var left = []
    var right = []
    var pivot = arr[0]

    for (var i = 1; i < arr.length; i++) {
      var item = arr[i]
      item > pivot ? right.push(item) : left.push(item)
    }

    if (target === pivot) {
      return true
    } else if (target > pivot) {
      arr = right
    } else {
      arr = left
    }
  }
}
```