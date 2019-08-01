title: fp in js
speaker: zgf

<slide class="aligncenter">

# 函数式编程与js {.text-landing}

<slide>

###  基于函数的编程范式{.content-center}

<br>

使用函数来抽象作用在数据之上的控制流和操作，从而在系统中消除副作用并减少对状态的改变{.content-center}

<slide>

###  y=f(x){.content-center}

<br>

y=f(g(x)){.content-center}

<br>

u=f(x)(y)(z){.content-center}

<slide>

```js
var p = document.createElement('p');
p.innerText = 'hello world';
document.body.appendChild(p);
```
<slide>

```js
function createEle(eleName) {
    var ele = document.createElement(eleName);
    return function(txt) {
        ele.innerText = txt
        return ele
    }
}
document.body.appendChild(createEle('p')('hello world'));
```

<slide :class="size-80" class="aligncenter">

:::column

#### 面向过程

---

#### 面向对象

---

#### 函数式

<slide :class="size-50" class="slide-top">

#### ·高阶函数{.content-center} 

<br>

函数可以接受一个函数作为参数，也可以返回一个函数<br><br>
回调、闭包、柯里化、组合

<slide :class="size-50" class="slide-top">

#### ·函数是一等公民{.content-center} 

<br>

函数是一种数据类型 同变量一样 可以赋值给其他变量 可以作为参数，也可以作为返回值

<!-- {.text-subtitle.animated.fadeIn.tobuild} -->

<slide :class="size-80" class="slide-top">

#### ·递归作为控制流{.content-center} 

```js
function fib_pp(n) {
	if (n == 1 || n == 2) {
		return 1;
	}
	var sum1 = 1, sum2 = 1;
	for (var i = 2; i <= n; i++) {
        var temp = sum1;
        sum1 = sum2;
        sum2 = temp + sum2;
	}
	return sum2;
}
console.log(fib_pp(5));

const fib_fp = (n) => n <=1 ? 1 : fib_fp(n - 1) + fib_fp(n -2)

console.log(fib_fp(5));
```
<slide :class="size-80" class="slide-top">

#### ·声明式 表达式 而非命令式语句{.content-center} 

<br>

```js
// 命令式方式
var array = [0, 1, 2, 3]
for(let i = 0; i < array.length; i++) {
    array[i] = Math.pow(array[i], 2)
}

array; // [0, 1, 4, 9]

// 声明式方式
[0, 1, 2, 3].map(num => Math.pow(num, 2)) // [0, 1, 4, 9]
```

<slide :class="size-50" class="slide-top">

#### ·纯函数{.content-center} 

<br>

幂等 输出只有入参决定 不受外部状态影响 也不影响外部状态

* 引用透明
* 无副作用
* 不修改状态

<slide :class="size-80" class="slide-middle">

:::column

```js
var a = 0;
function add() {
    return ++a
} 
add() // 1
```
---

```js
function add(x) {
    return ++x
}
add(0) // 1
```

<slide :class="size-50" class="slide-top">

### 优势{.content-center} 

<br>

* 代码简洁，接近数学语言
* 纯函数无副作用，易于调试
* 可复用，可维护性高
* 天然的并发友好
* 惰性求值（性能）

<slide :class="size-50" class="slide-top">

### 劣势{.content-center} 

<br>

* 递归的危险
* 函数嵌套 代码可读性降低 5+4+3-2 sub(sum(sum(5, 4), 3), 2）
* 惰性求值 垃圾回收 内存问题
* 效率

<slide :class="size-50" class="slide-top">

### Languages{.content-center} 

<br>

* Haskell  
* Common Lisp  
* Erlang 
* F#
* JavaScript
* Python

<slide :class="size-50" class="slide-top">

###  现状{.content-center} 

<br>

Functional programming has long been popular in academia, but with few industrial applications.

<slide :class="size-80" class="slide-top">

###  闭包{.content-center}

:::column

* 保留外部函数作用域内的局部变量 返回内层函数
* 持久化变量  模拟全局变量避免全局污染 缓存计算中间量
* 内存不友好

---

```js
// 创建一个闭包
function add() {
  let a = 0;

  return function() {
    return ++a;
  };
}

const counter = add();

console.log(counter());  // 1
console.log(counter());  // 2

```

<slide :class="size-50" class="slide-top">

###  高阶函数{.content-center} 

<br>

* map filter reduce
* 接受一个函数作为参数 遍历执行
* 降低副作用，简洁

```js
var list = [0, undefined, 1, 3, false]
list.filter(Boolean) //[1, 3]
```

<slide :class="size-50" class="slide-top">

###  组合{.content-center} 
<br>
合并多个函数，创建一个功能强大的新函数(lodash flow)

```js
function add(x, y) {
  return x + y;
}
function square(n) {
  return n * n;
}
var addSquare = _.flow(_.add, square);
addSquare(1, 2);
// => 9
```

<slide :class="size-70" class="slide-top">
```js
function flow(funcs) {
  const length = funcs ? funcs.length : 0
  let index = length
  while (index--) {
    if (typeof funcs[index] != 'function') {
      throw new TypeError('Expected a function')
    }
  }
  return function(...args) {
    let index = 0
    let result = length ? funcs[index].apply(this, args) : args[0]
    while (++index < length) {
      result = funcs[index].call(this, result)
    }
    return result
  }
}
```

<slide :class="size-80" class="slide-top">
###  柯里化{.content-center} 

:::column

* 把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数的函数
* 参数复用 延迟计算

---

```js
function multi(x, y) {
    return x * y
}

function curryingMulti(x) {
    return function (y) {
        return x * y
    }
}

console.log(multi(2, 4)); // 8
console.log(curryingMulti(2)(4)); // 8
```

<slide :class="size-70" class="slide-top">
```js
function curryingMulti(x) {
    return function (y) {
        return x * y
    }
}
var double = curryingMulti(2)

console.log(double(3)); // 6
console.log(double(5)); // 10
```

<slide :class="size-70" class="slide-top">
```js
function progressCurrying(fn, args) {
    var _this = this
    var len = fn.length;
    var args = args || [];
    return function() {
        var _args = Array.prototype.slice.call(arguments);
        Array.prototype.push.apply(args, _args);
        // 如果参数个数小于最初的fn.length，则递归调用，继续收集参数
        if (args.length < len) {
            return progressCurrying.call(_this, fn, args);
        }
        // 参数收集完毕，则执行fn
        return fn.apply(this, args);
    }
}
```

<slide :class="size-70" class="slide-top">

###  纯函数{.content-center} 
<br>
redux reducer

<br>
Changes are made with pure functions
To specify how the state tree is transformed by actions, you write pure reducers.

<br>
dispatch(action) ->reducer (payload, state) -> new state

<slide :class="size-50" class="aligncenter">

###  谢谢

