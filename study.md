## Types

### 1.1 字面量 Primitives: When you access a primitive type you work directly on its value.
* string
* number 
* boolean
* nulll
* undefined
* symbol

```javascript
const foo = 1;
let bar = 9;

bar = 9;

```
* Symbols cannot be faithfully polyfilled, so they should not be used when targeting browsers/environments that don't support them natively.

### 1.2 复合 Complex: When you access a complex type you work on a reference to its value.

* object 
* array
* function


```javascript
const foo = [1, 2];

const bar = foo;

```

## 引用 References

### 2.1 对于引用使用const 避免使用var

> Why? This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code

```javascript
// bad
var a = 1;
var b = 2;

// good
const a = 1;
const b = 2;
```
### 2.2 对于需要再赋值使用let 避免使用var

> Why? let is block-scoped rather than function-scoped like var.

```javascript
// bad
var count = 1;
if (true) {
  count += 1;
}

// good, use the let.
let count = 1;
if (true) {
  count += 1;
}
```

### 2.3 let const 块级作用域

```javascript
// const and let only exist in the blocks they are defined in.
{
  let a = 1;
  const b = 1;
}
console.log(a); // ReferenceError
console.log(b); // ReferenceError
```

## 对象 object

### 3.1 使用字面量创建对象

```javascript
// bad
const item = new Object();

// good
const item = {};
```

### 3.2 计算属性名当创建对象使用动态属性名

> Why? They allow you to define all the properties of an object in one place.

```javascript
function getKey(k) {
  return `a key named ${k}`;
}

// bad
const obj = {
  id: 5,
  name: 'San Francisco',
};
obj[getKey('enabled')] = true;

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
};
```

### 3.3 使用短名化对象的方法  方法不需要function关键字

```javascript
// bad
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```

### 3.4 使用短名化对象的属性值
```javascript
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```

### 3.5 短名化属性名成组 放在对象声明开始位置

```javascript
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

### 3.6 Only quote properties that are invalid identifiers

> Why? In general we consider it subjectively easier to read. It improves syntax highlighting, and is also more easily optimized by many JS engines.

```javascript
// bad
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
};

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

### 3.7 不直接Object.prototype方法 例如 hasOwnProperty propertyIsEnumerable isPrototypeOf

> Why? These methods may be shadowed by properties on the object in question - consider { hasOwnProperty: false } - or, the object may be a null object (Object.create(null)).

> 原型上方法被覆盖 或 对象被无原型创建

```javascript
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
/* or */
import has from 'has';
// ...
console.log(has.call(object, key));
```

### 3.8 使用扩展符代替`Object.assign` to shadow-copy(浅拷贝) objects  用剩余操作符获得一个新对象

> Prefer the object spread operator over Object.assign to shallow-copy objects. Use the object rest operator to get a new object with certain properties omitted.

```javascript
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
delete copy.a; // so does this

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

## Array

### 4.1 创建数组使用字面量方式

```javascript
// bad
const items = new Array();

// good
const items = [];
```

### 4.2 使用push代替直接赋值

```javascript
const someStack = [];

// bad
someStack[someStack.length] = 'abracadabra';

// good
someStack.push('abracadabra');
```

### 4.3 使用扩展符代替拷贝方式

```javascript
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

### 4.4 转array-like 对象成数组使用 扩展操作符 代替 Array.from

```javascript
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

### 4.5 对于可迭代对象循环使用Array.from而不是扩展操作符 因为生产一个中间数组

```javascript
// bad
const baz = [...foo].map(cbFn);

// good
const baz = Array.from(foo, cbFn);

```

> Array.from(arrayLike[, mapFn[, thisArg]]) Return value: A new Array instance.

### 4.6 对于数组回调方法使用return方法

* 对于回调本身只有一个语句 返回一个表达式并且无副作用

>Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement returning an expression without side effects

```javascript
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map(x => x + 1);

// bad - no returned value means `memo` becomes undefined after the first iteration
[[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
  const flatten = memo.concat(item);
  memo[index] = flatten;
});

// good
[[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
  const flatten = memo.concat(item);
  memo[index] = flatten;
  return flatten;
});

// bad
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});

// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }

  return false;
});
```

### 4.7  数组展现方式

> Use line breaks after open and before close array brackets if an array has multiple lines

```javascript
// bad
const arr = [
  [0, 1], [2, 3], [4, 5],
];

const objectInArray = [{
  id: 1,
}, {
  id: 2,
}];

const numberInArray = [
  1, 2,
];

// good
const arr = [[0, 1], [2, 3], [4, 5]];

const objectInArray = [
  {
    id: 1,
  },
  {
    id: 2,
  },
];

const numberInArray = [
  1,
  2,
];
```


## 解构 Destructuring

### 5.1 访问对象多个属性时候使用解构

> Why? Destructuring saves you from creating temporary references for those properties

```javascript
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

### 5.2 数组解构

```javascript
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```

### 5.3 返回值使用对象封装 便于对象解构 而不是数组解构

> Use object destructuring for multiple return values, not array destructuring

> Why? You can add new properties over time or change the order of things without breaking call sites.

```javascript
// bad
function processInput(input) {
  // then a miracle occurs
  return [left, right, top, bottom];
}

// the caller needs to think about the order of return data
const [left, __, top] = processInput(input);

// good
function processInput(input) {
  // then a miracle occurs
  return { left, right, top, bottom };
}

// the caller selects only the data they need
const { left, top } = processInput(input);
```

## Strings 字符串

### 6.1 使用单引号

```javascript
// bad
const name = "Capt. Janeway";

// bad - template literals should contain interpolation or newlines
const name = `Capt. Janeway`;

// good
const name = 'Capt. Janeway';

```

* 模板最好有修改值或者有换行的时候使用

### 6.2 100个字符之内不要换行

> Strings that cause the line to go over 100 characters should not be written across multiple lines using string concatenation.
> Why? Broken strings are painful to work with and make code less searchable.

```javascript
// bad
const errorMessage = 'This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.';

// bad
const errorMessage = 'This is a super long error that was thrown because ' +
  'of Batman. When you stop to think about how Batman had anything to do ' +
  'with this, you would get nowhere fast.';

// good
const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
```

### 6.3 使用模板方式代替字符串连接

> Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.

```javascript
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// bad
function sayHi(name) {
  return `How are you, ${ name }?`;
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

### 6.4 不要eval字符串 会引起xss

### 6.5 非必要不要在字符串进行转义

> Why? Backslashes harm readability, thus they should only be present when necessary.

```javascript
// bad
const foo = '\'this\' \i\s \"quoted\"';

// good
const foo = '\'this\' is "quoted"';
const foo = `my name is '${name}'`;
```

## 函数 Functions

### 7.1 命名函数表达式单体函数声明

> Why? Function declarations are hoisted, which means that it’s easy - too easy - to reference the function before it is defined in the file. This harms readability and maintainability. If you find that a function’s definition is large or complex enough that it is interfering with understanding the rest of the file, then perhaps it’s time to extract it to its own module! Don’t forget to explicitly name the expression, regardless of whether or not the name is inferred from the containing variable (which is often the case in modern browsers or when using compilers such as Babel). This eliminates any assumptions made about the Error's call stack. ([Discussion](https://github.com/airbnb/javascript/issues/794))

* 函数声明会提升 可读性和可维护性差 

```javascript
// bad
function foo() {
  // ...
}

// bad
const foo = function () {
  // ...
};

// good
// lexical name distinguished from the variable-referenced invocation(s)
const short = function longUniqueMoreDescriptiveLexicalFoo() {
  // ...
};
```

### 7.2 自执行匿名函数需要用圆括号包括

> Why?  An immediately invoked function expression is a single unit - wrapping both it, and its invocation parens, in parens, cleanly expresses this.


* Note that in a world with modules everywhere, you almost never need an IIFE.

```javascript
// immediately-invoked function expression (IIFE)
(function () {
  console.log('Welcome to the Internet. Please follow me.');
}());
```

### 7.3 函数声明不要在 if while etc

> Never declare a function in a non-function block (if, while, etc). Assign the function to a variable instead. Browsers will allow you to do it, but they all interpret it differently, which is bad news bears. 

* 不要在非函数块声明一个函数 例如 if while, 通过赋值一个变量来代替 浏览器允许这样 但是结果不同

### 7.4 函数声明不是语句

> ECMA-262 defines a block as a list of statements. A function declaration is not a statement.

```javascript
// bad
if (currentUser) {
  function test() {
    console.log('Nope.');
  }
}

// good
let test;
if (currentUser) {
  test = () => {
    console.log('Yup.');
  };
}
```

### 7.5 不要命名一个参数为`arguments`

> Never name a parameter arguments. This will take precedence over the arguments object that is given to every function scope.

```javascript
// bad
function foo(name, options, arguments) {
  // ...
}

// good
function foo(name, options, args) {
  // ...
}
```

### 7.6 不要`arguments` 选择使用剩余操作符`...`

```javascript
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```

### 7.7 使用默认参数

```javascript
// really bad
function handleThings(opts) {
  // No! We shouldn’t mutate function arguments.
  // Double bad: if opts is falsy it'll be set to an object which may
  // be what you want but it can introduce subtle bugs.
  opts = opts || {};
  // ...
}

// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
  // ...
}

// good
function handleThings(opts = {}) {
  // ...
}
```

### 7.8 避免使用默认参数的副作用

```javascript
var b = 1;
// bad
function count(a = b++) {
  console.log(a);
}
count();  // 1
count();  // 2
count(3); // 3
count();  // 3
```

### 7.9 默认参数放到参数最后

```javascript
// bad
function handleThings(opts = {}, name) {
  // ...
}

// good
function handleThings(name, opts = {}) {
  // ...
}
```

### 7.10 不要函数构造函数 创建一个函数

```javascript
// bad
var add = new Function('a', 'b', 'return a + b');

// still bad
var subtract = Function('a', 'b', 'return a - b');
```

### 7.11 在函数声明中在关键字function 之后有一个空格

> Why? Consistency is good, and you shouldn’t have to add or remove a space when adding or removing a name.

```javascript
// bad
const f = function(){};
const g = function (){};
const h = function() {};

// good
const x = function () {};
const y = function a() {};
```

### 7.12 参数尽量不要是对象

> Why? Manipulating objects passed in as parameters can cause unwanted variable side effects in the original caller.

```javascript
// bad
function f1(obj) {
  obj.key = 1;
}

// good
function f2(obj) {
  const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
}
```

### 7.13 不要再分配参数

> Why? Reassigning parameters can lead to unexpected behavior, especially when accessing the arguments object. It can also cause optimization issues, especially in V8.

```javascript
// bad
function f1(a) {
  a = 1;
  // ...
}

function f2(a) {
  if (!a) { a = 1; }
  // ...
}

// good
function f3(a) {
  const b = a || 1;
  // ...
}

function f4(a = 1) {
  // ...
}
```

### 7.14  剩余参数代替apply方式

*  Prefer the use of the spread operator ... to call variadic functions

> Why? It’s cleaner, you don’t need to supply a context, and you can not easily compose new with apply.

```javascript
// bad
const x = [1, 2, 3, 4, 5];
console.log.apply(console, x);

// good
const x = [1, 2, 3, 4, 5];
console.log(...x);

// bad
new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));

// good
new Date(...[2016, 8, 5]);
```

### 7.15 函数有多个参数 通过逗号分隔 最后一个函数也要有逗号

> Functions with multiline signatures, or invocations, should be indented just like every other multiline list in this guide: with each item on a line by itself, with a trailing comma on the last item.

```javascript
// bad
function foo(bar,
             baz,
             quux) {
  // ...
}

// good
function foo(
  bar,
  baz,
  quux,
) {
  // ...
}

// bad
console.log(foo,
  bar,
  baz);

// good
console.log(
  foo,
  bar,
  baz,
);
```

## 箭头函数 Arrow Functions

### 8.1 使用匿名函数最好是箭头函数

> When you must use an anonymous function (as when passing an inline callback), use arrow function notation.

>Why? It creates a version of the function that executes in the context of this, which is usually what you want, and is a more concise syntax.

>Why not? If you have a fairly complicated function, you might move that logic out into its own named function expression.

```javascript
// bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

