<a id="数组求2值和"></a>

## 数组求2值和

给定一个数组，一个期望值，找到数组中两个相加等于期望值

```js
function twoSum (nums = [], target = 0) {
     for (let i = 0, len = nums.length; i < len - 1; i++) {
          const index = nums.lastIndexOf(target - nums[i])
          if (index !== -1 && index !== i) {
               return [i, index]
          }
     }
}
```

<a id="数组内求目标和"></a>

## 数组内求目标和

给定一个不含重复数字的数组arr,指定个数n,目标和sum,判断是否含有由n个不同数字相加得到sum的情况

```js
function hasSum(arr = [], n = 1, sum = 0) {
     if (arr.length < n) {
          return false
     }
     for (let i = 0, len = arr.length; i < len; i++) {
          if (n === 1) {
               if (arr[i] === sum) {
                    return true
               }
          } else {
               return hasSum(arr.slice(i + 1), n - 1, sum - arr[i])
          }
     }
     return false
}
```