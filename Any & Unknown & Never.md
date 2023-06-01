# Any & Unknown & Never

## any

any 的本质是类型系统中的顶级类型，即 Top Type，

使用场景：不需要十分精确的类型标注，接受任意的类型。

典型实例：console.log 方法，无论传入字符串、数字和对象等，都支持打印，

```ts
function console.log(message?: any, ...optionalParams: any[]): void {}
```

为了避免 TypeScript 变成 AnyScript，需要记住的一些小 tips，

- 如果是类型不兼容报错导致你使用 any，考虑用类型断言替代
- 如果是类型太复杂导致你不想全部声明而使用 any，考虑将这一处的类型去断言为你需要的最简类型
- 如果你是想表达一个未知类型，更合理的方式是使用 unknown

## unknown

一个 unknown 类型的变量可以再次赋值为任意其它类型，但只能赋值给 any 与 unknown 类型的变量

```ts
let unknownValue: unknown;

let num: number = unknownValue; // Error

unknownValue = 1;

let anyValue: any = unknownValue;
let unknownValue2: unknown = unknownValue;
```

对 unknown 类型进行属性访问，需要进行类型断言，

```ts
let unknownVar: unknown;

unknownVar.foo(); // Error

(unknownVar as { foo: () => {} }).foo();
```

## never

never 类型被称为 Bottom Type，是整个类型系统层级中最底层的类型

- 联合类型中 never 类型会被忽略掉
- 只有 never 类型的变量能够赋值给另一个 never 类型变量
- 返回值为 never 说明无法执行到，比如 `throw Error()` 和 `while(true) {}`

## 类型断言

基本语法：`as NewType`，告诉编译器使用 `NewType` 进行类型检查。

类型断言的两种方式，

- `<>`，非 JSX 中
- `as`，JSX 中

**双重断言**：原类型与断言类型之间差异过大，先断言到 unknown/any 类型，再断言到预期类型

```ts
const str: string = "string";

(str as { handler: () => {} }).handler(); // Conversion of type 'string' to type '{ handler: () => {}; }' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.

(str as any as { handler: () => {} }).handler();
```

**非空断言**：通常用于对可选参数的访问，

```ts
declare const foo: {
  func?: () => { prop?: number | null };
};

foo.func().prop.toFixed(); // Cannot invoke an object which is possibly 'undefined'.

foo.func!().prop!.toFixed();

// 类似于可选链
foo.func?.().prop?.toFixed();
```

非空断言与可选链的区别在于：非空断言的运行时仍然会保持调用链，运行时可能会报错，而可选链则会在某一个部分收到 undefined 或 null 时直接短路掉

非空断言的常见场景： `document.querySelector、Array.find`，

```ts
const element = document.querySelector("#id")!;
const target = [1, 2, 3, 599].find((item) => item === 599)!;
```

类型断言在基于复杂接口实现对象时会很有用，比如，

```ts
interface IStruct {
  foo: string;
  bar: {
    barPropA: string;
    barPropB: number;
    barMethod: () => void;
    baz: {
      handler: () => Promise<void>;
    };
  };
}

// 直接指定为 IStruct 类型需要满足内部所有结构
const obj: IStruct = {}; // Type '{}' is missing the following properties from type 'IStruct': foo, bar

// 使用类型断言，可以在保留类型提示的前提下，不那么完整地实现这个结构
const bar: IStruct = <IStruct>{
  foo: "",
};

// 如果我们错误的实现了类型，类型提示仍然存在
const bar: IStruct = <IStruct>{
  foo: 1, // Conversion of type '{ foo: number; }' to type 'IStruct' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first. Property 'bar' is missing in type '{ foo: number; }' but required in type 'IStruct'.
};
```

类型断言的工作原理，实际上与类型的层级有关，

- 最顶级的类型，any 与 unknown
- 特殊的 Object ，它也包含了所有的类型，但和 Top Type 比还是差了一层
- String、Boolean、Number 这些装箱类型
- 原始类型与对象类型
- 字面量类型，即更精确的原始类型与对象类型嘛，需要注意的是 null 和 undefined 并不是字面量类型的子类型
- 最底层的 never

判断断言是否成立时，实际上是沿着类型的层级，看这两个类型是否能够找到一个公共的父类型。
