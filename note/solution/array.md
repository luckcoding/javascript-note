<a id="数组去重"></a>

## 数组去重

```js
// 第一种 filter + indexOf
function unique(arr = []) {
    return arr.filter(function (item, index, array) {
        // 当前值的下标与indexOf后的下表不相同，代表存在不止1个
        return array.indexOf(item) === index
    })
}

// 第一种 ES6 Set
function unique(arr) {
    return [...new Set(arr)]
}
```

---

<a id="数组乱序"></a>

## 数组乱序

```js
// 第一种，每个位置乱序的概率不同
function mixArr(arr = []) {
    return arr.sort(() => {
        return Math.random() - 0.5
    })
}

// 第二种，shuffle 洗牌算法
function shuffle (arr = []) {
    let m = arr.length
    while(m > 1) {
        let index = parseInt(Math.random() * m--);
        [arr[index], arr[m]] = [arr[m], arr[index]]
        // 每次取最后一个数，与剩下的数中的随机一个值交换位置
    }
    return arr
}
```

<a id="数组扁平化"></a>

## 数组扁平化

```js
function flatten(arr = []) {
    var result = []
    for (var i = 0; i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
            result = result.concat(flatten(arr[i]))
        } else {
            result.push(arr[i])
        }
    }
    return result
}
```
