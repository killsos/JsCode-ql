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

### 8.2  函数只有一个`return`语句 适合箭头函数

> If the function body consists of a single statement returning an expression without side effects, omit the braces and use the implicit return. Otherwise, keep the braces and use a return statement.

>Why? Syntactic sugar. It reads well when multiple functions are chained together.

```javascript
// bad
[1, 2, 3].map(number => {
  const nextNumber = number + 1;
  `A string containing the ${nextNumber}.`;
});

// good
[1, 2, 3].map(number => `A string containing the ${number}.`);

// good
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  return `A string containing the ${nextNumber}.`;
});

// good
[1, 2, 3].map((number, index) => ({
  [index]: number,
}));

// No implicit return with side effects
function foo(callback) {
  const val = callback();
  if (val === true) {
    // Do something if callback returns true
  }
}

let bool = false;

// bad
foo(() => bool = true);

// good
foo(() => {
  bool = true;
});

```

### 8.3 箭头函数多行时候用圆括号包起来便于可读

```javascript
// bad
['get', 'post', 'put'].map(httpMethod => Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
);

// good
['get', 'post', 'put'].map(httpMethod => (
  Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
));
```

### 8.4 箭头函数简洁

* 如果只有一个参数 省略参数圆括号

* 只要参数用圆括号包起来 函数体用花括号包括起来

```javascript
// bad
[1, 2, 3].map((x) => x * x);

// good
[1, 2, 3].map(x => x * x);

// good
[1, 2, 3].map(number => (
  `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
));

// bad
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

### 8.5 避免 => 与比较操作符 >= 或者 <=

```javascript
// bad
const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;

// bad
const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;

// good
const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);

// good
const itemHeight = (item) => {
  const { height, largeSize, smallSize } = item;
  return height > 256 ? largeSize : smallSize;
};
```

## 类与构造器

### 9.1 用类避免使用prototype

```javascript
// bad
function Queue(contents = []) {
  this.queue = [...contents];
}
Queue.prototype.pop = function () {
  const value = this.queue[0];
  this.queue.splice(0, 1);
  return value;
};

// good
class Queue {
  constructor(contents = []) {
    this.queue = [...contents];
  }
  pop() {
    const value = this.queue[0];
    this.queue.splice(0, 1);
    return value;
  }
}
```
### 9.2 使用extends继承

```javascript
/ bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function () {
  return this.queue[0];
};

// good
class PeekableQueue extends Queue {
  peek() {
    return this.queue[0];
  }
}
```

### 9.3 `return this`来实现链式调用

>Methods can return this to help with method chaining.

```javascript
// bad
Jedi.prototype.jump = function () {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function (height) {
  this.height = height;
};

const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined

// good
class Jedi {
  jump() {
    this.jumping = true;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }
}

const luke = new Jedi();

luke.jump()
  .setHeight(20);
```

### 9.4 声明toString方法时候一定没有副作用

>It’s okay to write a custom toString() method, just make sure it works successfully and causes no side effects.

```javascript
class Jedi {
  constructor(options = {}) {
    this.name = options.name || 'no name';
  }

  getName() {
    return this.name;
  }

  toString() {
    return `Jedi - ${this.getName()}`;
  }
}
```

### 9.5 类中默认constructor 如果特定的声明

* 如果空的constructor函数 无需写

>Classes have a default constructor if one is not specified. An empty constructor function or one that just delegates to a parent class is unnecessary.

```javascript
// bad
class Jedi {
  constructor() {}

  getName() {
    return this.name;
  }
}

// bad
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
  }
}

// good
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
    this.name = 'Rey';
  }
}
```

### 9.6 Avoid duplicate class members

> Why? Duplicate class member declarations will silently prefer the last one - having duplicates is almost certainly a bug.

```javascript
// bad
class Foo {
  bar() { return 1; }
  bar() { return 2; }
}

// good
class Foo {
  bar() { return 1; }
}

// good
class Foo {
  bar() { return 2; }
}
```

## Modules 模块

### 10.1  使用 import /export

>Always use modules (import/export) over a non-standard module system. You can always transpile to your preferred module system.

```javascript
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;

// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;

// best
import { es6 } from './AirbnbStyleGuide';
export default es6;

```

### 10.2 不要import使用通配符

>Do not use wildcard imports

>Why? This makes sure you have a single default export.

```javascript
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';

// good
import AirbnbStyleGuide from './AirbnbStyleGuide';
```

### 10.3 不要直接输出引入的文件

> Why? Although the one-liner is concise, having one clear way to import and one clear way to export makes things consistent.

```javascript
// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';

// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

### 10.4 仅从一个文件导入

> Why? Having multiple lines that import from the same path can make code harder to maintai

```javascript
// bad
import foo from 'foo';
// … some other imports … //
import { named1, named2 } from 'foo';

// good
import foo, { named1, named2 } from 'foo';

// good
import foo, {
  named1,
  named2,
} from 'foo';
```

### 10.5 Do not export mutable bindings

>Why? Mutation should be avoided in general, but in particular when exporting mutable bindings. While this technique may be needed for some special cases, in general, only constant references should be exported.

```javascript
// bad
let foo = 3;
export { foo };

// good
const foo = 3;
export { foo };
```

### 10.6 模块中只有一个输出 尽量默认输出

>In modules with a single export, prefer default export over named export

```javascript
// bad
export function foo() {}

// good
export default function foo() {}
```

### 10.7 Put all imports above non-import statements

> Why? Since imports are hoisted, keeping them all at the top prevents surprising behavior.

```javascript
// bad
import foo from 'foo';
foo.init();

import bar from 'bar';

// good
import foo from 'foo';
import bar from 'bar';

foo.init();
```
### 10.8 多个引入如数组处理

>Multiline imports should be indented just like multiline array and object literals.

>Why? The curly braces follow the same indentation rules as every other curly brace block in the style guide, as do the trailing commas.

```javascript
// bad
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';

// good
import {
  longNameA,
  longNameB,
  longNameC,
  longNameD,
  longNameE,
} from 'path';

```
### 10.9 不能用webPack loader syntax在import

>Disallow Webpack loader syntax in module import statements

>Why? Since using Webpack syntax in the imports couples the code to a module bundler. Prefer using the loader syntax in webpack.config.js.

```javascript
// bad
import fooSass from 'css!sass!foo.scss';
import barCss from 'style!css!bar.css';

// good
import fooSass from 'foo.scss';
import barCss from 'bar.css';
```


## Iterators and Generators

### 11.1 不要迭代 用高阶函数替代循环如 forin forof 

> Don’t use iterators. Prefer JavaScript’s higher-order functions instead of loops like for-in or for-of.

> Why? This enforces our immutable rule. Dealing with pure functions that return values is easier to reason about than side effects.

> 这是强迫我们不变的规则 用纯函数处理通过return返回值是简单的

* Use map() / every() / filter() / find() / findIndex() / reduce() / some() / ... to iterate over arrays, and Object.keys() / Object.values() / Object.entries() to produce arrays so you can iterate over objects.

```javascript
const numbers = [1, 2, 3, 4, 5];

// bad
let sum = 0;
for (let num of numbers) {
  sum += num;
}
sum === 15;

// good
let sum = 0;
numbers.forEach((num) => {
  sum += num;
});
sum === 15;

// best (use the functional force)
const sum = numbers.reduce((total, num) => total + num, 0);
sum === 15;

// bad
const increasedByOne = [];
for (let i = 0; i < numbers.length; i++) {
  increasedByOne.push(numbers[i] + 1);
}

// good
const increasedByOne = [];
numbers.forEach((num) => {
  increasedByOne.push(num + 1);
});

// best (keeping it functional)
const increasedByOne = numbers.map(num => num + 1);
```

### 11.2 不要用generators现在

>Why? They don’t transpile well to ES5.


### 11.3 想要generator函数必须 命名规则

>If you must use generators, or if you disregard our advice, make sure their function signature is spaced properly.

>Why? function and * are part of the same conceptual keyword - * is not a modifier for function, function* is a unique construct, different from function.

```javascript
// bad
function * foo() {
  // ...
}

// bad
const bar = function * () {
  // ...
};

// bad
const baz = function *() {
  // ...
};

// bad
const quux = function*() {
  // ...
};

// bad
function*foo() {
  // ...
}

// bad
function *foo() {
  // ...
}

// very bad
function
*
foo() {
  // ...
}

// very bad
const wat = function
*
() {
  // ...
};

// good
function* foo() {
  // ...
}

// good
const foo = function* () {
  // ...
};
```

## Properties

### 12.1 Use dot notation when accessing properties

```javascript
const luke = {
  jedi: true,
  age: 28,
};

// bad
const isJedi = luke['jedi'];

// good
const isJedi = luke.jedi;
```

### 12.2 Use bracket notation [] when accessing properties with a variable.

```javascript
onst luke = {
  jedi: true,
  age: 28,
};

function getProp(prop) {
  return luke[prop];
}

const isJedi = getProp('jedi');
```

### 用幂运算符**

```
// bad
const binary = Math.pow(2, 10);

// good
const binary = 2 ** 10;
```

## Variables 变量

### 13.1 用const、let声明变量 不要生成全局变量

> Always use const or let to declare variables. Not doing so will result in global variables. We want to avoid polluting the global namespace.

```javascript
// bad
superPower = new SuperPower();

// good
const superPower = new SuperPower();
```

### 13.2 用const、let声明变量

>Why? It’s easier to add new variable declarations this way, and you never have to worry about swapping out a ; for a , or introducing punctuation-only diffs. You can also step through each declaration with the debugger, instead of jumping through all of them at once.

```javascript
// bad
const items = getItems(),
    goSportsTeam = true,
    dragonball = 'z';

// bad
// (compare to above, and try to spot the mistake)
const items = getItems(),
    goSportsTeam = true;
    dragonball = 'z';

// good
const items = getItems();
const goSportsTeam = true;
const dragonball = 'z';
```

### 13.3 let、const 各自成组

```javascript
// bad
let i, len, dragonball,
    items = getItems(),
    goSportsTeam = true;

// bad
let i;
const items = getItems();
let dragonball;
const goSportsTeam = true;
let len;

// good
const goSportsTeam = true;
const items = getItems();
let dragonball;
let i;
let length;
```

### 13.4 Assign variables where you need them, but place them in a reasonable place.

> Why? let and const are block scoped and not function scoped.

```javascript
// bad - unnecessary function call
function checkName(hasName) {
  const name = getName();

  if (hasName === 'test') {
    return false;
  }

  if (name === 'test') {
    this.setName('');
    return false;
  }

  return name;
}

// good
function checkName(hasName) {
  if (hasName === 'test') {
    return false;
  }

  const name = getName();

  if (name === 'test') {
    this.setName('');
    return false;
  }

  return name;
}
```
### 13.5 不要链式赋值

>Why? Chaining variable assignments creates implicit global variables.

```javascript
// bad
(function example() {
  // JavaScript interprets this as
  // let a = ( b = ( c = 1 ) );
  // The let keyword only applies to variable a; variables b and c become
  // global variables.
  let a = b = c = 1;
}());

console.log(a); // throws ReferenceError
console.log(b); // 1
console.log(c); // 1

// good
(function example() {
  let a = 1;
  let b = a;
  let c = a;
}());

console.log(a); // throws ReferenceError
console.log(b); // throws ReferenceError
console.log(c); // throws ReferenceError

// the same applies for `const`
```

### 13.6 避免使用自增或自减

> Why? Per the eslint documentation, unary increment and decrement statements are subject to automatic semicolon insertion and can cause silent errors with incrementing or decrementing values within an application. It is also more expressive to mutate your values with statements like num += 1 instead of num++ or num ++. Disallowing unary increment and decrement statements also prevents you from pre-incrementing/pre-decrementing values unintentionally which can also cause unexpected behavior in your programs

```javascript
/ bad

const array = [1, 2, 3];
let num = 1;
num++;
--num;

let sum = 0;
let truthyCount = 0;
for (let i = 0; i < array.length; i++) {
  let value = array[i];
  sum += value;
  if (value) {
    truthyCount++;
  }
}

// good

const array = [1, 2, 3];
let num = 1;
num += 1;
num -= 1;

const sum = array.reduce((a, b) => a + b, 0);
const truthyCount = array.filter(Boolean).length;
```

## Hoisting 提升

### 14.1 var->hoisted  const/let->TDZ

```javascript
// we know this wouldn’t work (assuming there
// is no notDefined global variable)
function example() {
  console.log(notDefined); // => throws a ReferenceError
}

// creating a variable declaration after you
// reference the variable will work due to
// variable hoisting. Note: the assignment
// value of `true` is not hoisted.
function example() {
  console.log(declaredButNotAssigned); // => undefined
  var declaredButNotAssigned = true;
}

// the interpreter is hoisting the variable
// declaration to the top of the scope,
// which means our example could be rewritten as:
function example() {
  let declaredButNotAssigned;
  console.log(declaredButNotAssigned); // => undefined
  declaredButNotAssigned = true;
}

// using const and let
function example() {
  console.log(declaredButNotAssigned); // => throws a ReferenceError
  console.log(typeof declaredButNotAssigned); // => throws a ReferenceError
  const declaredButNotAssigned = true;
}
```

### 14.2 Anonymous function expressions hoist their variable name, but not the function assignment

```javascript
function example() {
  console.log(anonymous); // => undefined

  anonymous(); // => TypeError anonymous is not a function

  var anonymous = function () {
    console.log('anonymous function expression');
  };
}
```

### 14.3 Named function expressions hoist the variable name, not the function name or the function body

```javascript
function example() {
  console.log(named); // => undefined

  named(); // => TypeError named is not a function

  superPower(); // => ReferenceError superPower is not defined

  var named = function superPower() {
    console.log('Flying');
  };
}

// the same is true when the function name
// is the same as the variable name.
function example() {
  console.log(named); // => undefined

  named(); // => TypeError named is not a function

  var named = function named() {
    console.log('named');
  };
}
```

### 函数声明方式 提升

>Function declarations hoist their name and the function body.

```javascript
function example() {
  superPower(); // => Flying

  function superPower() {
    console.log('Flying');
  }
}
```

## 比较运算符与相等

### 15.1 Use === and !== over == and !=

### 15.2 条件语句 强制转布尔值

>Conditional statements such as the if statement evaluate their expression using coercion with the ToBoolean abstract method and always follow these simple rules:

* Objects evaluate to true
* Undefined evaluates to false
* Null evaluates to false
* Booleans evaluate to the value of the boolean
* Numbers evaluate to false if +0, -0, or NaN, otherwise true
* Strings evaluate to false if an empty string '', otherwise true

* Object 为 true

* undefined 为 false

* Null 为 false

* 布尔值

* 数字中 0 NaN 为false

* 字符串 中 空字符串为false

* 数组也是对象

### 15.3 Use shortcuts for booleans, but explicit comparisons for strings and numbers

* 用明确值 对于字符串和数字 需要明确的判断条件

```javascript
// bad
if (isValid === true) {
  // ...
}

// good
if (isValid) {
  // ...
}

// bad
if (name) {
  // ...
}

// good
if (name !== '') {
  // ...
}

// bad
if (collection.length) {
  // ...
}

// good
if (collection.length > 0) {
  // ...
}
```

### 15.4 more [Truth, Equality and JavaScript](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108)


### 15.5 case default 用let/const 声明class function

>Use braces to create blocks in case and default clauses that contain lexical declarations


>Why? Lexical declarations are visible in the entire switch block but only get initialized when assigned, which only happens when its case is reached. This causes problems when multiple case clauses attempt to define the same thing


```javascript
// bad
switch (foo) {
  case 1:
    let x = 1;
    break;
  case 2:
    const y = 2;
    break;
  case 3:
    function f() {
      // ...
    }
    break;
  default:
    class C {}
}

// good
switch (foo) {
  case 1: {
    let x = 1;
    break;
  }
  case 2: {
    const y = 2;
    break;
  }
  case 3: {
    function f() {
      // ...
    }
    break;
  }
  case 4:
    bar();
    break;
  default: {
    class C {}
  }
}
```

### 15.6 条件表达式不要嵌套

> Ternaries should not be nested and generally be single line expressions.

```javascript
// bad
const foo = maybe1 > maybe2
  ? "bar"
  : value1 > value2 ? "baz" : null;

// split into 2 separated ternary expressions
const maybeNull = value1 > value2 ? 'baz' : null;

// better
const foo = maybe1 > maybe2
  ? 'bar'
  : maybeNull;

// best
const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
```

### 15.7 避免不必要的语句 使用表达式替换

```javascript
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```

### 15.8 Enclose operators in parentheses when they are mixed in a statement. When mixing arithmetic operators, do not mix ** and % with themselves or with +, -, *, & /

```javascript
// bad
const foo = a && b < 0 || c > 0 || d + 1 === 0;

// bad
const bar = a ** b - 5 % d;

// bad
if (a || b && c) {
  return d;
}

// good
const foo = (a && b < 0) || c > 0 || (d + 1 === 0);

// good
const bar = (a ** b) - (5 % d);

// good
if ((a || b) && c) {
  return d;
}

// good
const bar = a + b / c * d;
```


## 块

### 16.1 使用大括号包含对个语句

```javascript
// bad
if (test)
  return false;

// good
if (test) return false;

// good
if (test) {
  return false;
}

// bad
function foo() { return false; }

// good
function bar() {
  return false;
}
```

### 16.2 if else 同列

```javascript
// bad
if (test) {
  thing1();
  thing2();
}
else {
  thing3();
}

// good
if (test) {
  thing1();
  thing2();
} else {
  thing3();
}
```

### 16.3 如果if中return else就不需要了

```javascript
// bad
function foo() {
  if (x) {
    return x;
  } else {
    return y;
  }
}

// bad
function cats() {
  if (x) {
    return x;
  } else if (y) {
    return y;
  }
}

// bad
function dogs() {
  if (x) {
    return x;
  } else {
    if (y) {
      return y;
    }
  }
}

// good
function foo() {
  if (x) {
    return x;
  }

  return y;
}

// good
function cats() {
  if (x) {
    return x;
  }

  if (y) {
    return y;
  }
}

//good
function dogs(x) {
  if (x) {
    if (z) {
      return y;
    }
  } else {
    return z;
  }
}
```

## Control Statements 控制语句

```javascript
// bad
if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
  thing1();
}

// bad
if (foo === 123 &&
  bar === 'abc') {
  thing1();
}

// bad
if (foo === 123
  && bar === 'abc') {
  thing1();
}

// bad
if (
  foo === 123 &&
  bar === 'abc'
) {
  thing1();
}

// good
if (
  foo === 123
  && bar === 'abc'
) {
  thing1();
}

// good
if (
  (foo === 123 || bar === "abc")
  && doesItLookGoodWhenItBecomesThatLong()
  && isThisReallyHappening()
) {
  thing1();
}

// good
if (foo === 123 && bar === 'abc') {
  thing1();
}
  ```

## 注释

### 18.1 多行用/** */


```javascript
// bad
// make() returns a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {

  // ...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}
```

### 18.2 当行用//

>Place single line comments on a newline above the subject of the comment. Put an empty line before the comment unless it’s on the first line of a block.

```javascript
// bad
const active = true;  // is current tab

// good
// is current tab
const active = true;

// bad
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// good
function getType() {
  console.log('fetching type...');

  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// also good
function getType() {
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}
```

### 18.3 注释后面有空格

>Start all comments with a space to make it easier to read

```javascript
// bad
//is current tab
const active = true;

// good
// is current tab
const active = true;

// bad
/**
 *make() returns a new element
 *based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}
```
### 18.4 注释中TODO FIXED

>Prefixing your comments with FIXME or TODO helps other developers quickly understand if you're pointing out a problem that needs to be revisited, or if you're suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable.

>FIXME: -- need to figure this out or TODO: -- need to implement.

### 18.5 Use // FIXME: to annotate problems.

```javascript

class Calculator extends Abacus {
  constructor() {
    super();

    // FIXME: shouldn’t use a global here
    total = 0;
  }
}

```
### 18.6 Use // TODO: to annotate solutions to problems.

```javascript

class Calculator extends Abacus {
  constructor() {
    super();

    // TODO: total should be configurable by an options param
    this.total = 0;
  }
}

```