# personal-algorithm

> 面试中会遇到一些笔试或者手写一些代码.下面是我收集的一些  

* 求一个字符串中出现次数最多的字符

```JavaScript
 function index(str) {
            var json = {};　　　
            for (var j = 0; j < str.length; j++) {
                if (!json[str.charAt(j)]) {
                    json[str.charAt(j)] = 1;
                } else {
                    json[str.charAt(j)]++;
                }
            };
            var iMax = 0;
            var iIndex = "";
            for (var i in json) {
                if (json[i] > iMax) {
                    iMax = json[i];
                    iIndex = i
                }
            }
        }
```

* 冒泡排序

```JavaScript
 function bubbleSort(arr) {
            for (let i = 0, l = arr.length; i < l - 1; i++) {
                for (let j = i + 1; j < l; j++) {
                    if (arr[i] < arr[j]) {
                        let tem = arr[i];
                        arr[i] = arr[j];
                        arr[j] = tem;
                    }
                }
            }
            return arr;
        }
```

* 快速排序

```JavaScript
function quickSort(arr){
            //如果数组<=1,则直接返回
            if(arr.length<=1){return arr;}
            var pivotIndex=Math.floor(arr.length/2);
            //找基准，并把基准从原数组删除
            var pivot=arr.splice(pivotIndex,1)[0];
            //定义左右数组
            var left=[];
            var right=[];

            //比基准小的放在left，比基准大的放在right
            for(var i=0;i<arr.length;i++){
                if(arr[i]<=pivot){
                    left.push(arr[i]);
                }
                else{
                    right.push(arr[i]);
                }
            }
            //递归
            return quickSort(left).concat([pivot],quickSort(right));
        }      
```

* 对数组内的数字进行随机排序

```javascript
// 时间复杂度 o(n*n)
function roa(arr) {
  const ret = []
  for (let i=0,len =arr.length; i<len; i++) {
      const n = Math.floor(Math.random() * arr.length);
      ret.push(arr[n]);
      arr.splice(n, 1);
  }
  return ret;
}

// 时间复杂度 o(n)
function shuffle(a) {
  for (let i = 0, len=a.length; i < len - 1; i++) {
      const index = parseInt(Math.random() * (len - i));
      const temp = a[index];
      a[index] = a[len - i - 1];
      a[len - i - 1] = temp;
  }
  return a
}

```

* 数组去重(`ES6`提供的有`Set`构造函数)

```JavaScript
    function moveSame(arr) {
        let temp = {}
        return arr.filter(item => {
            if (!temp[item]) {
                temp[item] = 1
                return true
            }
            return false
        })
    }
```

* js的拷贝: 深拷贝和浅拷贝`Objective.assign()`是浅拷贝

```JavaScript
function deepCopy(source){
 var result = {};
 for(var i in source){
 if(typeof source[i] === "object"){
  result[i] = deepCopy(source[i]);
 }else{
  result[i] = source[i];
 }
 }
 return result;
}
```

* 函数的防抖, 多次的操作合并成一次计算,适用于实时搜索，拖拽，登录用户名密码格式验证等等

```JavaScript
function debounce(fn, delay) {
      let timer = null
      return function(...args) {
       timer && clearTimeout(timer);
        timer = setTimeout(() => {
          timer = null
          fn.apply(this, args)
        }, delay)
      }
    }
```

* 函数的节流,使用的场景有窗口调整，页面滚动，抢购疯狂点击等等。

```JavaScript
function throttle(fn, wait) {
      let startTime, timer = null
        return function (...args) {
          const now = Date.now();
          !startTime && (startTime = now);
          timer && clearTimeout(timer);
          if ((now - startTime) >= wait) {
            fn.apply(this, args)
            startTime = now
          } else {
            timer = setTimeout(() => {
              timer = null
              fn.apply(this, args)
            }, delay)
          }
        }
    }
```

#### 生成几位的随机数

```JavaScript
function getRandomNum(n) { 
  if (!Number(n) || n == 0) { 
    throw new Error('参数不对')
  }
  n = Number(n)
  let min, max
  min = Number(1 + '0'.repeat(n - 1))
  if (n === 1) {
    min = 0
  }
  max = Number('9'.repeat(n))
  return Math.floor(Math.random()*(max-min))+min;
}
```

#mydiary/interview/algorithm