# 迭代器模式
迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素。

## 实现自己的迭代器
实现一个each函数，each函数接受2个参数，第一个为被循环的数组，第二个为循环中的每一步后将被触发的回调函数：
var each = function (ary, callback) {
	for (var i = 0, l = ary.length; i < l; i++) {
	 callback.call(ary[i], i, ary[i]) // 把下标和元素当做参数传给callback函数
  }
}
each([1, 2, 3], function(i, n) {
  alert([i, n])
})

## 7.3内部迭代器和外部迭代器
迭代器可以分为内部迭代器和外部迭代器，他们有各自的适用场景
1. 内部迭代器

我们刚刚编写的each函数属于内部迭代器，each函数的内部已经定义好了迭代规则，它完全接手整个迭代过程，外部只需要一次初始调用

内部迭代器在调用的时候非常方便，外界不用关心迭代器内部的实现，跟迭代器的交互也仅仅是一次初始调用，但这也刚好是内部迭代器的缺点。由于内部迭代器的迭代规则已经被提前规定，上面的each函数就无法同时迭代2个数组了。

比如现在有个需求，要判断2个数组里元素的值是否完全相等，如果不改写each函数本身的代码，我们能够入手的地方似乎只剩下each的回调函数了，代码如下：
```
var compare = function (ary1, ary2) {
  if (ary1.length != ary2.length) {
    throw new Error('ary1和ary2不相等')
  }
  each(ary1, function(i, n) {
    if (n != ary2[i]) {
      throw new Errow('ary1和ary2不相等')
    }
  })
  alert('ary1和ary2不相等')
}
compare([1, 2, 3], [1, 2, 4]) // throw new Errow('ary1和ary2不相等')
```

2.外部迭代器

外部迭代器必须显式的请求迭代下一个请求元素

外部迭代器增加了一些调用的复杂度，但相对页增强了迭代器的灵活性，我们可以手工控制迭代的过程或者顺序
```
var Iterator = function (obj) {
  var current = 0
  var next = function () {
    current += 1
  }
  var isDone = function () {
    return current >= obj.length
  }
  var getCurrItem = function () {
    return obj[current]
  }
  return {
    next: next,
    isDone: isDone,
    getCurrItem: getCurrItem
  }
}
var compare = function (iterator1, iterator2) {
  while(!iterator1.isDone() && !iterator2.isDone()) {
    if (iterator.getCurrItem() !== iterator2.getCurrItem()) {
      throw new Error('iterator1和iterator2不相等')
    }
    iterator1.next()
    iterator2.next()
  }
  alert('iterator1和iterator2相等')
}
var iterator1 = Iterator([1, 2, 3])
var iterator2 = Iterator([1, 2, 3])
compare(iterator1, iterator2) // 输出iterator1和iterator2相等
```

## 7.4 迭代类数组对象和字面量对象
迭代器模式不仅可以迭代数组，还可以迭代一些类数组的对象，比如arguments、{"0": 'a', "1": 'b'}等。通过上面的代码可以观察到，无论是内部迭代器还是外部迭代器，只要被迭代的聚合对象拥有length属性而且可以用下标访问，那它就可以迭代。

在javascript中，for in语句可以用来迭代普通字面量对象的属性，
```
$each = function (obj, callback) {
  var value,
  i = 0,
  length = obj.length,
  isArray = isArraylike(obj)
  if (isArray) { // 迭代类数组
    for (; i < length; i++) {
      value = callback.call(obj[i], i, obj[i])
      if (value === false) {
        break
      }
    }
  } else {
    for (i in obj) { // 迭代object对象
      value = callback.call(obj[i], i, obj[i])
      if (value === false) {
        break
      }
    }
  }
  return obj
}
```
## 倒叙迭代器
由于GoF中迭代器模式的定义非常松散，所以我们可以有多种多样的迭代器实现。总的来说，迭代器模式提供了循环访问一个聚合对象中每个元素的方法，但它没有规定我们以顺序、倒序还是中序来循环遍历聚合对象。
```
var reverseEach = function (ary, callback) {
  for (var l = ary.length -1; l >= 0; l--) {
    callback(l,ary[l])
  }
}
reverseEach([0,1,2], function (i, n) {
  console.log(n) // 分别输出2,1,0
})
```
## 中止迭代器
迭代器可以向普通for循环中的break一样，提供一种跳出循环的方法，each函数里有这样一句
```
if (value === false) {
  break
}
这句代码的意思是，约定如果回调函数的执行结果返回false,则提前终止循环
var each = function (ary, callback) {
  for (var i = 0, l = ary.length; i< l; i++) {
    if (callback(i, ary[i]) === false) { // callback的执行结果返回false,提前终止迭代
      break
    }
  }
}
each([1, 2, 3, 4, 5], function (i, n) {
  if (n > 3) { // n大于3的时候终止循环
    return false
  }
})
```