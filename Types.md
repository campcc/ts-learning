# Types

Primitive and Object

## Vs JavaScript

- number / string / boolean / null / undefined / object
- ECMAScript 2015（ES6）引入的 symbol
- 2020 (ES11) 引入的 bigint

上述类型，除了 null 和 undefined 外，余下的基本和 ts 中类型对应，

```ts
const name: string = "lee";
const age: number = 24;
const male: boolean = false;
const undef: undefined = undefined;
const nul: null = null;
const obj: object = { name, age, male };
const bigintVar1: bigint = 9007199254740991n;
const bigintVar2: bigint = BigInt(9007199254740991);
const symbolVar: symbol = Symbol("unique");
```

## null & undefined

JavaScript 中 null 为空值，undefiend 表示未定义，ts 中 null 与 undefined 类型都是有具体意义的类型，其中，

- 开启 strictNullChecks 检查的情况下，作为两种独立的类型
- 不开启 strictNullChecks 检查的情况下，被视作其他类型的子类型

```ts
const tmp1: null = null;
const tmp2: undefined = undefined;

const tmp3: string = null; // 仅在关闭 strictNullChecks 时成立，下同
const tmp4: string = undefined;
```

## void

JavaScript 中 void 是一个关键词，常用于 void expression，

```js
//
void 0 === undefined;

// 强制将后面的函数声明转化为表达式
void (function iife() {
  console.log("iife");
})();
```

ts 中 void 用于描述一个内部没有 return 语句，或者没有显式 return 一个值的函数的返回值，

```ts
// undefined 能够被赋值给 void 类型的变量，就像在 JavaScript 中一个没有返回值的函数会默认返回一个 undefined
const voidVar1: void = undefined;

const voidVar2: void = null; // 需要关闭 strictNullChecks
```

## 数组

ts 中声明数组可以使用两种方式，两种方式完全等价，更常用的是第一种，

```ts
let arr1: number[] = [1, 2, 3];
let arr2: Array<number> = [1, 2, 3];
```

某些情况下，使用元组（Tuple）来代替数组更为恰当，

```ts
let arr1: [string, string, string] = ["1", "2", "3"];
let arr2: [string, string?, string?] = ["lee"];
let arr3: [name: string, age: number, male?: boolean] = ["lee", 18, true];
```

元组的好处，

- 显式的越界访问，如通过下标访问，会产生一个类型错误，数组无法检测
- 隐式的越界访问，如解构赋值，会产生一个警告，数组无法检测

## 对象

TypeScript 中使用特殊的类型标注（interface）来描述对象类型，这里 “描述” 的规则是，

- 值 & 类型，每一个属性的值必须一一对应到接口的属性类型
- 严格对应，不能有多的属性，也不能有少的属性，包括直接在对象内部声明，或是 `obj1.other = 'xxx'` 这样属性访问赋值的形式

属性定义支持修饰，常见的有可选（Optional）和只读（Readonly）

```ts
interface IDescription {
  name?: string;
  age: number;
  readonly male: boolean;
}

const obj1: IDescription = {
  name: "linbudu",
  age: 599,
  male: true,
};
```

## type & interface

大部分场景下接口结构（interface）都可以被类型别名（type）所取代，两者都可以用来描述对象，一般比较推荐的做法是，

- interface 用来描述对象、类的结构
- type 用来描述函数签名、联合类型、工具类型等

## object & Object & {}

Object、Boolean、Number、String、Symbol 等属于装箱类型（Boxed Types），在 ts 中包含了一些超出预期的类型，以 String 为例，包括 undefined、null、void，以及代表的 拆箱类型（Unboxed Types） string，

所以，**在任何情况下，你都不应该使用这些装箱类型**。

`object` 的引入就是为了解决对 Object 类型的错误使用，它代表**所有非原始类型的类型，即数组、对象与函数类型**，

`{}` 部无属性定义的空对象，类似 `new Object()`，意味着任何非 null / undefined 的值，也应该避免使用，从这个层面上看，使用它和使用 any 一样恶劣

## unique symbol

与 JavaScript 中的 Symbol 不同，ts 中需要使用 unique symbol 来创建一个唯一的 Symbol 值

```ts
const uniqueSymbolFoo: unique symbol = Symbol("lee");
```
