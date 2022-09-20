+++
title = "读《Effective TypeScript》"
date = 2022-09-20
[taxonomies]
categories = ["Tech"]
tags = ["reading", "typescript"]
[extra]
toc = false
+++

《Effective TypeScipt》是一本评价很好的 TS 相关书籍，书中介绍了 62 个类型相关的技巧/建议，对理解 TypeScript 的类型系统以及如何应对一些日常开发中会遇到的实际问题很有帮助。
我看的英文版，很易读，和读技术文档差别不大。总之这本书还是很推荐的。

以下是我在读的过程中记的一些要点笔记：

---

### 1-6: Getting to Know TypeScript

- TS 是 JS 的超集，它保留了大部分 JS 的运行时行为模型，一小部分 JS 允许的行为在 TS 中不被允许，另外一些则可以通过编译器 flag 来控制是否保留

- TS 的类型系统不是 sound 的，类型不保证正确

- 两个最重要的编译配置：`noImplicitAny` `strictNullChecks`

- `tsc`本身就可以执行‘转译’，生成旧版本的 js 代码

- 类型检查和代码生成是独立的

- class 同时引入了一个*类型*和一个*值*，使得可以在运行时执行类型检查等操作，如`instanceof`

- TS 的类型信息只作用于编译时，在运行时被‘擦除’，零运行时开销

- Tagged Union 是一种在运行时引入‘类型’信息的方法

- TS 的类型系统是**结构化类型/鸭子类型**的，TS 中的类型是 open 的，而不是 precise 的，实际的(通过了类型检查的)值可能会有类型声明中没有的属性

- class 也是结构化的，可以将一个满足某个类型形状的值声明为该 class 类型，但原型链并没有连上

- 结构化类型在测试 mock 的场景中很有用

- avoid `any`!

### 7: Think of Types as Sets of Values

- 从范畴论的角度来说，类型是值的集合

- `never`类型用来表示没有值的集合的类型，即空集，是最小的集合

- 只有一个值的集合所表示的类型也叫单位类型 / unit type

- `keyof (A&B) = (keyof A) | (keyof B)`

- `keyof (A|B) = (keyof A) & (keyof B)`

- 从集合的角度理解`extends`关键字：某类型的一个子类型，（一个包含在内的、更小的集合）

- tuple 类型的类型信息中还有一个 length 属性，所以`[T,T,T]`不能赋值给`[T,T]`

- Think of “extends,” “assignable to,” and “subtype of ” as synonyms for “subset of.”

### 8: Know How to Tell Whether a Symbol Is in the Tyep Sapce or Value Space

- TS 中的类型标识符和值标识符互不冲突，可以同名

- `class`和`enum`同时引入了一个类型和一个值

- `typeof`操作符只能作用于值，但其结果用于类型上下文和值上下文时有不同的意义

- 对`class`标识符应用`typeof`的结果取决于上下文

- `typeof`、`this`以及其他许多操作符和关键字在类型和值的上下文中有不同的意义

### 9: Prefer Type Declartions to Type Assertions

- 有两种方式给一个值进行类型注解：`const val: Person = {}` `const cal = {} as Person`

- 尽可能使用类型声明而不是类型断言

- 仅在确定自己比 type checker 有更多的类型信息时才使用类型断言，通常是在和外部系统交互时

- `!`非空断言也是类型断言，同样应该在自己拥有更多信息时才使用

- 通过类型断言进行的类型转换不是任意的，仅当其中一个是另一个的子集时才可以，`unkown`类型是任意类型的子集

- 通过注解箭头函数的返回值来进行类型声明：

- `const people = ['alice', 'bob'].map((ame): Person => ({ name }));`

### 10: Avoid Object Wrapper Types

### 11: Recognize the Limits of Excess Prperty Checking

- 尽管是结构化类型系统，Ts 提供了“excess propety checking”检测来帮助发现错误，它作用于对象字面量，（这意味着如果先把对象字面量赋值给一个变量，再用这个变量的话就不会触发该检查）

- 如果不想要这种检查，可以在类型声明中加上 index signature

- 另一个在结构化类型系统之外的检查发生在 weak type 上，指的是那些全部属性均是 optional 的类型，TS 检查值类型和声明类型至少有一个共同属性，这个检查作用于所有赋值，而不仅仅是字面量

- 这两种检查均是在结构化类型对可赋值性检查之外的检查，有助于帮助发现 typo 和其他一些涉及属性名的错误，在类型有可选字段时尤其有用

### 12: Apply Types to Entire Function Expressions When Possible

- 考虑对整个函数表达式应用一个类型注解，而不是对参数和返回值进行注解

- `const fn: XxxFn = (param) => returned;`

- 如果有多个函数的类型注解相同，考虑这种方式来减少重复，另一个好处是使逻辑意图更加显式

- 如果是库作者，考虑对常见 callback 提供整个函数的类型

- 使用`typeof fn`来得到另一个函数的类型来使用

### 13: Know the Differences Between type and interface

- 总体上 type 比 interface 更加强大，尤其是涉及到 union type 时，元组和数组类型用 type 也更容易表达

- interface 相比 type 的优点在于它可以被*增强*，interface 可以通过*声明合并*的方式来增加字段，如果不希望用户增加字段，则用 type

- 如何选择：

  - 复杂类型如 union 只能用 type

  - 如果是简单的类型，考虑两点：风格一致性、是否希望增强

  - 如果类型是要对外可见/发布的，用户可能会希望能够增强它，prefer interface

  - 如果类型仅仅是内部使用，声明合并通常不是希望的，prefer type

### 14: Use Type Operations and Generics to Avoid Repeating Yourself

- DRY 也同样应该应用在类型方面，类型上的重复更加常见，因为人们不熟悉为减少类型重复而采用的模式

- 最简单的减少重复的方式：命名你的类型，这类似于在代码中提取一个常量而不是到处重复形成魔数，函数类型同样适用，将签名相同的函数的类型提取出来

- 如果两个类型有相同的部分字段，可以尝试提取出一个基类，然后 extends

- 相反，如果要使用一个基类型的某几个字段来定义一个衍生类型，可以使用索引该基类型的方式来定义：`type TopNavState = { userId: State['userId']; title: State['title']; }`，使用映射类型来进一步减少重复：`type TopNavState = { [k in 'userId' | 'title']: State[K] }`,这类似于代码中的通过循环减少重复，标准库对此常见模式有支持，那就是`type Pick<T, K extends keyof T> = { [k in K]: T[k] };`，上面例子改写成：`type TopNavState = Pick<State, 'userId' | 'title'>`，使用 pick 这样的泛型类型类似于调用一个函数

- 提取 Tagged Union 类型的 tag 时使用类型字段索引，而不是重复书写 `type ActionType = Action['type']; // 'save' | 'load' | '...'`

- `type OptionsUpdate = { [K in keyof Options]?: Option[K] };` 标准库提供`Partial<T>`

- 如果要从一个值生成类型，使用`typeof`，但是通常是先有类型再有值

- `type ReturnedT = ReturnType<typeof XxxFn>;`，同样应当小心使用

- 通过`extends`关键字来对泛型参数进行约束

### 15: Use Index Signatures for Dynamic Data

- 索引签名用在动态数据的场景中，例如解析 CSV，列名提前不知道`{ [columnName: string]: string }[]`

- 动态数据没有保证，考虑对值的类型曾加一个 undefined 来确保使用之前进行运行时检查

- 如果提前知道字段信息，使用该信息而不是索引签名，可以用可选字段或 union 的形式

- 如果使用 string 来作为索引键的类型太广的话，考虑：

  - Record，例如如`Record<'x' | 'y', number>`，键类型降到 string 的一个子集

  - Mapped Type，可以在 Record 的基础上对不同的键指定不同的类型：

    - `type Vec3D = { [k in 'x' | 'y' | 'z']: number }`

    - `type ABC = { [k in 'a' | 'b']: k extends 'b' ? string : number }`

### 16: Prefer Arrays, Tuples, and ArrayLike to number Index Signatures

- 数组的索引在运行时实际上也是 string 类型的，但 TS 将其类型声明为 number，有助于帮助发现错误，number index signatures 仅仅是一个 TS 构造

- As a general rule, there’s not much reason to use number as the index signature of a type rather than string. If you want to specify something that will be indexed using numbers, you probably want to use an Array or tuple type instead.

- 标准库提供`ArrayLike<T>`类型，只有 length 属性和 number index signature

### 17: Use readonly to Avoid Errors Associated with Mutation

- `number[]`是`readonly number[]`的子类型（而不是反过来），所以`number[]`类型的值可以被赋值给`readonly number[]`类型的变量，反过来则不行

- 如果函数有不改变参数的值的规约的话，应当对参数的类型加上`readonly`修饰符

- `readonly`在类型层面上指定值的内容不能修改，变量的指向能否更改仍然由`let/const`控制

- `readonly` is shallow

### 18: Use Mapped Types to Keep Values in Sync

- 如果你想要一个对象有和另一个对象完全一致的属性，使用 mapped types

- 保持同步：如果对象的每个属性都对应一个类似 flag 的东西，添加属性时需要同步更新 flag，考虑使用 mapped type 来增加一个值，这样修改后如果忘记更新会报类型错误，这样就强制了同步这个操作

- `const REQUIRES_UPDATE: {[k in keyof ScatterProps]: boolean} = {xs:true, xy: true onClick:false};`

### 19: Avoid Cluttering Your Code with Inferable Types

- 手动注解可以被推断的类型总体上不被推荐

- 函数签名，包括参数和返回值最好进行类型注解，函数体内的本地变量最好不注解

- 如果函数参数有默认值，这时该参数的类型注解也应当省略

- 如果函数是作为回调来使用，通常已经在那里被注解了，因此此时不需要再显式注解

- 对象字面量可以被 infer 出来，但最好显式注解一个命名类型，这还会触发 excess property checking

- 函数返回值类似，能够被 infer，但最好显式注解

### 20: Use Different Variables for Different Types

- 丢弃 js 的动态类型特性

- 增加变量来表示不同的类型语义，而不是重用同一个变量去改变它的类型，有很多好处

  - 更加语义化，不同的变量可以区分出不同的语义，更加具体的变量命名

  - 有助于使用类型推断，而不是显式类型注解

  - 更简单的类型，而不是 union

  - 使用 const 而不是 let

### 21: Understand Type Widening

- 类型扩展发生在 ts 尝试通过赋值给变量的值来推断变量的类型时

- 使用 const 而不是 let 来定义变量就可以阻止变量通过之后的赋值去扩展类型范围

- 通过显式类型注解、更多的上下文信息、`as const`等方式来阻止类型猜测

### 22: Understand Type Narrowing

- TS 通过条件判断和控制流分析来进行类型收缩

- 使用 tagged/discriminated union 来帮助进行类型收缩

- 使用自定义类型谓词函数来在 map/filter/reduce 等地方进行类型收缩

### 23: Create Objects All at Once

- prefer creating objects all at once, rather than piece by piece

- 使用`...`操作符来一次性构造出对象：`const point = {...pt, ...id}`

- 如果必须分步骤构建，尝试引入中间变量，这样最后的变量可以一次给对

- 如果要条件地添加一个属性，需要使用一个辅助函数 `p98`

### 24: Be Consisitent in Your Use of Aliases

- 这里的 alias 指引用同一个对象的多个变量，会引入不一致

- if you introduce an alias, use it consistently

- 使用解构语法来防止给一个属性不同的名字

- 对一个值进行的某个属性信息类型判断后，在这个值之上的函数调用可能会更改这个属性，但 ts 控制流分析不会重新验证去反映出这个变化

### 25: Use async Functions Instead of Callbacks for Asynchronous Code

- `Promise.race()`返回值的类型是各个 promise 的类型的 union

- prefer `async/await` to raw promises

- 异步函数总是返回 promise

- 一个函数应当总是被同步地调用或总是被异步地调用，而不是混用，异步函数可以强制保证这一点，回调和原生 promise 容易导致半异步的函数

- 如果一个函数返回一个 promise，将其声明为 async

### 26: Understand How Context Is Used in Type Inference

- 值所在的上下文会影响类型推断，一些在 JS 中结果相同的写法在 TS 中可能导致推断出不同的类型

- TS 在一个值最初被引入时即确定其类型，而不是根据其最终被使用时的上下文，这就可能导致推断出的类型跟最终被使用时希望的类型不一致，例如

  - 使用 let 声明的字符串的类型是 string，使用时期待的类型是 literal

  - 定义字面量数组时其类型为数组而不是元组，这时应当使用显式类型注解或`as const`，注意`as const`还会加上`readonly`

  - 设计字面量和元组的对象也有相同的问题，同样使用显式类型注解或`as const`

### 27: Use Functional Constructs and Libraries to Help Types Flow

- 函数式风格的库/构件的 TS 类型声明天然使得类型 flow through

- 使用内置的函数式构件和第三方函数式类库来增强类型流，增加易读性，减少显式类型注解

### 28: Prefer Types That Always Represent Valid States

- 保持状态总是*有效/合法*状态，设计良好的状态能表达出的所有组合都是合理/有效的

### 29: Be Liberal in What You Accept and Strict in What You Produce

- 参数类型可以更 open 一些，有多种不同的形式，这样函数用起来更*方便*

- 返回值类型需要更加精确，应当只有一种形状

- 可以为参数和返回值设计相关但又不同的类型，一种更规范，用于返回值，一种更宽松，用于参数

### 30: Don’t Repeat Type Information in Documentation

- 避免在文档和变量名中重复类型信息

- 单位信息如果不能通过类型表达出来的话，可以加在变量名中

### 31: Push Null Values to the Perimeter of Your Types

- 返回一整个完全不为空的对象或者 null，而不是多个相互关联的可能为 null 可能不为 null 的值

- 考虑将类的字段声明为都不为 null，然后用一个静态方法去拿到所有值后再去构造，而不是以 null 作为初始值去构造

### 32: Prefer Unions of Interfaces to Interfaces of Unions

- 如果一个类型的多个属性都是 union，通常是错误的抽象，会导致非法状态的出现

- If you can represent a data type in TypeScript with a tagged union, it’s usually a good idea to do so

### 33: Prefer More Precise Alternatives to String Types

- Avoid “stringly typed”

- 命名类型还可以用`/** */`来加文档注释

### 34: Prefer Incomplete Types to Inaccurate Types

- 增加类型精确性的同时也导致出现不正确的可能性增大

- 避免过于精确的类型导致使用/理解起来过去复杂，尽量避免导致自动补全失效的改动

### 35: Generate Types from APIs and Sepcs, Not Data

- 避免从样例数据生成类型

### 36: Name Types Using the Language of Your Problem Domain

- 避免不恰当的命名

### 37: Consider “Brands” for Nominal Typing

- TS 中让类型变为 Nonminal Type 的方式是为类型增加一个独特的标记字段，称为`brand`，这会引入运行时开销

- 也可以仅在类型层面上使用这些标记，还可以将内置类型变为 nominal 的，需要结合类型谓词/断言来使用，结合运行时的断言，可以做到类型系统原本无法做到的一些事

### 38: Use the Narrowest Possible Scope for `any` Types

- 需要类型强转的时候使用`as any`而不是将类型注解改为`any`，这样不会把变量在其他地方也搞成`any`

- 永远不要将返回类型声明为`any`

### 39: Prefer More Precise Variants of `any` to Plain `any`

- `any[]`

- `{[key: string]: any}` `Record<string, any>` `object`

- `() => any` `(...args: any[]) => any`

### 40: Hide Unsafe Type Assertions in Well-Typed Functions

- 有一些函数的签名是类型安全的，但具体实现较难做到完全类型安全

- 可以考虑将不安全的类型断言操作隐藏在一个签名类型正确的函数内部

### 41: Understand Evolving `any`

- 如果一个变量根据初始赋值被推断为`any`，会根据后续使用情况变推断成更具体的类型

- 最好还是显式地进行类型注解

### 42: Use `unknown` Instead of `any` for Values with an Unknown Type

- `unknown`是一个比`any`更类型安全的类型，如果不知道值的类型，最好用`unknown`

- `unknown`可以强制值的使用者去做类型断言或运行时类型检查

- `{}` `object` `unknown`

### 43: Prefer Type-Safe Approaches to Monkey Patching

- 尽可能避免在全局对象/宿主环境对象上加属性这种操作

- 如果避免不了，可以用 interface 的声明合并功能，或 extends 出一个更具体的类型然后再断言

### 44: Track Your Type Coverage to Prevent Regressions in Type Safety

- `npx type-coverage`

### 45: Put TypeScript and @types in devDependencies

### 46: Understand the Three Versions Involved in Type Declarations

- 依赖管理涉及到的版本问题：包本身，包类型声明(@types)，TSC

- type 的 patch 版本可以比包本身的高

- 确保更新库时同步更新相应的@types

- 如果库本身就是用 TS 写的，可以将类型声明和库一起打包，虽然也可能有一些问题；如果不是，最好是用 DefinitelyTyped 来维护

### 47: Export All Types That Appear in Public APIs

- 虽然一些出现在 API 中但没有显式导出的类型可以被库的用户手动提取出来，但最好还是将这些类型都显式地导出比较好

### 48: Use TSDoc for API Comments

- `/** */`

- `@param`

- `@return`

- TS 就不要再加`{type}`了

- 支持 markdown

- 避免过于 verbose

### 49: Provide a Type for `this` in Callbacks

- 如果作为 API 的回调函数中限制了 this 的值/类型，需要在函数签名中指定 this 的类型`this: XXXType`

### 50: Prefer Conditional Types to Overloaded Declarations

- `function double<T extends number | string>(x: T): T extends string ? string : number {...}`

- Conditional types distribute over unions

### 51: Mirror Types to Sever Dependencies

- Don’t force JS users to depend on @types, Don’t force web developers to depend on NodeJS

- 可以利用结构化类型将第三方库的类型 mirror 成自己的

### 52: Be Aware of the Pitfalls of Testing Types

- 测试类型时注意 equality 和 assignability 的区别

- 使用 dtslint 之类的工具

### 53: Prefer ECMAScript Features to TypeScript Features

- TS 的部分功能是为了弥补 JS 的缺失，随着 JS 的发展，其中一些不再需要了，现在应该仅将 TS 定位在 type 层面上来使用，避免使用 enum 等

- prefer 使用字面量 union 来作为枚举

- parameter preoerties 会生成一些运行时代码

- 使用 ES 模块，而不是 namespace 和三斜杠引用

- 装饰器仍然未稳定，尝试避免使用

### 54: Know How to Iterate Over Objects

- `let k: keyof T`&`for-in` 当对象属性已知时，注意这种方式可能产生运行时错误，比如实际对象包含类型声明之外的属性、对象原型链上有可枚举的属性

- `Object.entries()`

### 55: Understand the DOM hierarchy

- `EventTarget -> Node -> Element -> HTMLElement -> HTMLBUttonElement`

### 56: Don’t Rely on Private to Hide Information

- `private`仅仅是一个类型层面的约束，不要过于依赖

### 57: Use Source Maps to Debug TypeScript

- `“sourceMap”: true`

### 58: Write Modern JavaScript

- tsc 会执行转译，尽量用最新的 JS 特性

### 59: Use @ts-check and JSDoc to Experiment with Typescript

- `@ts-check`

- 不要纠结于把 JSDoc 全写对，目标应该是转成 TS 文件

### 60: Use allowJs to Mix TypeScript and JavaScript

- `allowJs`

### 61: Convert Module by Module Up Your Dependency Graph

- 一个模块一个模块来

- 首先是第三方依赖模块，@types

- 从依赖图的底层开始向上依次进行

### 62: Don’t Condider Migration Complete Until You Enable noImplicitAny

- 将所有隐式 any 去除后才算完全转到 TS
