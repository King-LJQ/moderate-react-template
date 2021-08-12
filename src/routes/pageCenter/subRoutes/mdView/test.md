## API
| a | b |
| - | - |
### PreviewLayout
| a | b |
| - | - |
| 参数     | 说明                       | 类型 | 默认值 | 版本  |
| :------- | :------------------------- | :--: | :----: | :---: |
| children | 传递的组件，可以是任意组件 | jsx  |  null  | 0.1.0 |

### MdPreviewer

| 参数 | 说明          |  类型  | 默认值 | 版本  |
| :--- | :------------ | :----: | :----: | :---: |
| md   | markdown 文档 | string |  null  | 0.1.0 |

### CodePreviewer

| 参数     | 说明           |  类型  | 默认值 | 版本  |
| :------- | :------------- | :----: | :----: | :---: |
| code     | 要显示的代码   | string |  null  | 0.0.1 |
| showCode | 是否要展示代码 |  bool  |  true  | 0.1.0 |

<p align="center">
  <a href="https://github.com/uiwjs/react-markdown-editor/issues">
    <img src="https://img.shields.io/github/issues/uiwjs/react-markdown-editor.svg">
  </a>
  <a href="https://github.com/uiwjs/react-markdown-editor/network">
    <img src="https://img.shields.io/github/forks/uiwjs/react-markdown-editor.svg">
  </a>
  <a href="https://github.com/uiwjs/react-markdown-editor/stargazers">
    <img src="https://img.shields.io/github/stars/uiwjs/react-markdown-editor.svg">
  </a>
  <a href="https://github.com/uiwjs/react-markdown-editor/releases">
    <img src="https://img.shields.io/github/release/uiwjs/react-markdown-editor.svg">
  </a>
  <a href="https://www.npmjs.com/package/@uiw/react-markdown-editor">
    <img src="https://img.shields.io/npm/v/@uiw/react-markdown-editor.svg">
  </a>
</p>

<p align="center">
  A markdown editor with preview, implemented with React.js and TypeScript.
</p>

## Install

```bash
npm i @uiw/react-markdown-editor
```

## Document

Official document [demo preview](https://uiwjs.github.io/react-markdown-editor/) ([🇨🇳 中国镜像网站](http://uiw.gitee.io/react-markdown-editor/))

## Basic Usage

```jsx
import MarkdownEditor from "@uiw/react-markdown-editor";
import React from "react";
import ReactDOM from "react-dom";

const Dome = () => (
  <MarkdownEditor value={this.state.markdown} onChange={this.updateMarkdown} />
);
```

controlled usage

```jsx
import MarkdownEditor from "@uiw/react-markdown-editor";
import React from "react";
import ReactDOM from "react-dom";

class App extends React.Component {
  constructor() {
    super();
    this.state = {
      markdown: "# This is a H1  \n## This is a H2  \n###### This is a H6",
    };
    this.updateMarkdown = this.updateMarkdown.bind(this);
  }

  updateMarkdown(editor, data, value) {
    this.setState({ markdown: value });
  }

  render() {
    return (
      <MarkdownEditor
        value={this.state.markdown}
        onChange={this.updateMarkdown}
      />
    );
  }
}

ReactDOM.render(<App />, document.getElementById("app"));
```

## Props

- value (_string_) - the raw markdown that will be converted to html (**required**)
- `visble?:boolean` - Shows a preview that will be converted to html.
- `toolbars?:array` - Tool display settings.
- `toolbarsMode?:array` - Tool display settings.
- onChange (_function(editor: IInstance, data: CodeMirror.EditorChange, value: string)_) - called when a change is made (**required**)

> [Other Props Options](https://github.com/uiwjs/react-markdown-editor/blob/8de6abbf628b6d272d7da1c28e985fbbcba71b93/src/components/CodeMirror/index.tsx#L21-L60)

### Development

```bash
npm run dev
npm run type-check:watch
npm run doc
```

## License

[MIT © Kenny Wong](./LICENSE)

> this 是在调用时被绑定的,完全取决于函数的调用位置(也就是函数的调用方法)

# 2.1 调用位置

函数在代码中被调用的位置(而不是声明的位置)

找调用位置实际上在找“函数被调用的问题”

那就要分析调用栈

# 2.2 绑定规则

找到了调用位置了再通过`四条绑定`规则来判断 this 的绑定对象。
四条绑定：默认绑定，隐式绑定，强制绑定，new 绑定。

## 2.2.1 默认绑定

无法应用其他规则时的默认规则。

---

> 声明在全局作用域中的变量(比如 var a = 2)就是全局对象的一个同名属性。它们本质上就是同一个东西,并不是通过复制得到的,就像一个硬币的两面一样。

起初我看这句话觉得是在说废话。。。，现在看来事实上这件事确实是一件值得注意的，我忽略了，作用域对象居然和 window 居然"重合"了,一般情况下，this 指向的对象是不能和作用域混淆的，也就是说你无法通过 this 得到作用域的中的对象，切记。

---

如果使用严格模式(strict mode) ,那么全局对象将无法使用默认绑定,因此 this 会绑定到 undefined。
说严格模式，实际上确切的讲是针对的函数在严格模式下就行，（这种情况不常见，一般是在引入的其他方库，严格情况不一致导致）

## 2.2.2 隐式绑定

判断调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含。

---

不过这种说法可能会造成一些`误导`。

- 误导：

```js
function foo() {
  console.log(this.a);
}
var obj = {
  a: 2,
  foo: foo,
};
obj.foo(); // 2
```

思考一下这个 foo 和 obj 的关系。
严格讲无论是在 obj 中定义还是先定义再添加为引用属性，
严格讲都不属于 obj 的对象。

_~`误导的意思我觉得是有一种foo完全属于obj这个上下文对象的错觉`~_

---

> 对象属性引用链中只有最顶层或者说最后一层会影响调用位置

这句话也值得推敲，简单讲如果多层对象嵌套了，那么隐式绑定遵循的就是就近原则，绑定最近的对象

---

**隐式丢失**：丢失绑定对象的情况会很常见，然后就会使用默认绑定（也就是说会绑定到 window 或者 undifind，取决于是否为严格模式）
思考下面代码：

```html
<div>
  <section>asdasdasd</section>
</div>
```

```jsx
import { PreviewLayout } from "react-code-previewer";

<PreviewLayout>asdasdasd</PreviewLayout>;
```

```js
function foo() {
  console.log(this.a);
}
var obj = {
  a: 2,
  foo: foo,
};
var bar = obj.foo;
// 函数别名!
var a = "oops, global";
// a 是全局对象的属性
bar();
// "oops, global"
```

主要看这句

```js
var bar = obj.foo;
bar();
```

bar 是 obj.foo 的一个引用，但实际上引用是 foo 函数本身，也就是说没有进行任何绑定情况的下的函数，那么这样的"干净"的函数被调用，自然就采用了默认绑定。

---

那么更微妙的一种情况就是通过参数传入回调函数，比如

```js
function doFoo(fn) {
  // fn 其实引用的是 foo
  fn(); // <-- 调用位置!
}
doFoo(obj.foo);
```

参数传递是一种隐式赋值。
也就是说跟上面的赋值没什么区别，结果也就一样，都是丢失了。

不要停，还没完，进而又引出了对 setTimeout 的思考。

```js
setTimeout( obj.foo, 100 ); // "oops, global"
JavaScript 环境中内置的 setTimeout()
函数实现和下面的伪代码类似:
function setTimeout(fn,delay) {
	// 等待 delay 毫秒
	fn(); // <-- 调用位置!
}
```

这不就是 setTimeout 为啥丢失 this 的原因了。。。。
_~`但有一说一，this丢失确实给人一种奇怪之感，主要还是说setTimeout这种api具体实现不了解，造成的问题难以下手，了解了大概的实现原理之后，问题也就迎刃而解了，但这就有个思考，使用一个api除了知道怎么使用以外还有必要知道具体实现么？具体的实现用得着暴露给我一个普通的使用者么？我的想法就是一个api在使用的时候尽可能的没啥问题，比如setTimeout丢失this的问题，能不能“静默解决了”，可能有点极端了，但也算一种进化的方向，现在的箭头函数就是填补了这个空，让你不要去考虑了，但问题还是那样，我觉得最好就是setTimeout内部直接存储其调用位置的this给这个回调函数，但可能至今它不去改，保持这个“原生态”，应该是有其深意`~_

描述一下实现隐私绑定的过程：一个对象内部包含一个指向函数的属性,并通过这个属性间接引用函数,从而把 this 间接(隐式)绑定到这个对象上。

## 2.2.3 显式绑定

通过针对的隐私绑定的实现描述，引出了一个想法，如果不想在对象内部包含函数引用=》不想包含了，有办法么？
当然，使用 call 和 apply。
而且书上强调了

> 严格来说,JavaScript 的宿主环境有时会提供一些非常特殊的函数,它们并没有这两个方法。但是这样的函数非常罕见,JavaScript 提供的绝大多数函数以及你自己创建的所有函数都可以使用 call(..) 和 apply(..) 方法。

so，放心用吧。

---

**装箱**:如果你传入了一个原始值(字符串类型、布尔类型或者数字类型)来当作 this 的绑定对象,这个原始值会被转换成它的对象形式(也就是 new String(..)、new Boolean(..) 或者 new Number(..)) 。这通常被称为“装箱” 。

_~`其实你可以试试在console中定一个原始值，然后看下隐式原型`~_
]![[Pasted image 20210502122313.png]]
估计这就是装箱了，有种按需的意思了。。。。

---

但是书上说了：

> 可惜,显式绑定仍然无法解决我们之前提出的丢失绑定问题。

思考一下，为啥这么说呢，“丢失绑定问题”究竟是需要解决什么？
_~`我觉得奥，通过分析比较隐式和显式绑定，得出实际上要解决的是：将一个已经绑定好的函数通过一个变量进行引用并调用之后，依然能保持之前绑定好的状态。那么也就引出了接下来要讲的的硬绑定-bind等办法`~_

---

- 硬绑定：显式的强制绑定到一个对象，bind
- 函数提供的 context 的参数，比如 foreach 的第二参数，其实内部用的就是 call 和 apply

## 2.2.4 new 绑定

> JavaScript 也有一个 new 操作符,使用方法看起来也和那些面向类的语言一样,绝大多数开发者都认为 JavaScript 中 new 的机制也和那些语言一样。然而,JavaScript 中 new 的机制实际上和面向类的语言完全不同。

js 中需要重新定义一个构造函数：

> js 中，构造函数是一些使用 new 操作符时被调用的函数。它们并不会属于某个类,也不会实例化一个类。实际上, 它们甚至都不能说是一种特殊的函数类型,它们只是被 new 操作符调用的普通函数而已=》就是一个被 new 调用的函数

实际上并不存在所谓的“构造函数” ,只有对于函数的“构造调用”

1. 创建(或者说构造)一个全新的对象。
2. 这个新对象会被执行 [[原型]] 连接。
3. 这个新对象会绑定到函数调用的 this。
4. 如果函数没有返回其他对象,那么 new 表达式中的函数调用会自动返回这个新对象。

# 2.3 优先级

new>硬绑定>隐式绑定>默认绑定

# 2.4 绑定例外

## 2.4.1 被忽略的 this

bind(..) 可以对参数进行柯里化(预先设置一些参数) ,这种方法有时非常有用
如果函数并不关心 this 的话,你仍然需要传入一个占位值

**更安全的 this**：如果函数并不关心 this 的话,你仍然需要传入一个占位值

## 2.4.2 间接引用

之前谈过的通过直接赋值或者传参数这种间接赋值的都属于此类情况

## 2.4.3 软绑定

硬绑定会有些问题：
问题在于,硬绑定会**大大**降低函数的灵活性,使用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this。

```js
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function (obj) {
    var fn = this; // 捕获所有 curried 参数
    var curried = [].slice.call(arguments, 1);
    var bound = function () {
      return fn.apply(
        !this || this === (window || global) ? obj : this,
        curried.concat.apply(curried, arguments)
      );
    };
    bound.prototype = Object.create(fn.prototype);
    return bound;
  };
}
```

在 Function 的原型上追加了一个 softBind 的函数
就是传入一个对象作为默认绑定的对象，防止默认绑定 window 或者 undifind，这种情况就跟 bind 相同，然后依然保留了函数的的隐式和显式绑定修改 this 的能力

# 2.5 this 词法

> 箭头函数不使用 this 的四种标准规则,而是根据外层(函数**或者**全局)作用域来决定 this。

`foo() 内部创建的箭头函数会捕获调用时 foo() 的 this`
`箭头函数的绑定无法被修改。 (new 也不行!)`

# 2.6 小结

正常的函数调用使用四规则

注意一些调用无意中使用了默认规则的情况：主要就是赋值的情况

忽略 this 最安全的方法是传一个 Object.create.(null)，以保护 window 不被篡改

es6 不使用四条规则，而是根据外层函数调用的 this 来确定绑定，ES6 之前用 self = this 来实现，机制几乎一致
