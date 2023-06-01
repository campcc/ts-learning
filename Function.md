# Function

变量的类型描述了变量值的类型，函数的类型描述函数的入参和返回值。

## Type Signature

函数的类型签名，一般有两种声明方式，

- 在函数中进行参数和返回值的类型声明
- 使用类型别名将函数声明抽离出来

```ts
// ways 1
const foo: (name: string) => number = function (name) {
  return name.length;
};

// ways2
type FuncFoo = (name: string) => number;

const foo: FuncFoo = (name) => {
  return name.length;
};
```

接口中的函数类型签名，

```ts
interface Foo {
  (name: string) => number;
}
```

## Return Type: undefined & void

对于函数返回值而言，undefined 和 void 的使用场景如下，

- 如果函数没有返回值，其返回值类型为 void
- 如果函数进行了返回操作，但没有返回实际的值，其返回值类型为 undefined

```ts
function foo(): void {}

function bar(): undefined {
  return;
}
```

## optional & rest params

函数参数支持可选参数（?）和剩余参数（...），需要注意的是：**可选参数必须位于必选参数之后**，

```ts
// 可选参数必须位于必选参数之后，本质的原因是 JavaScript 函数的入参是按照位置形参的方式传入的，而不是按参数名
// 剩余参数的类型为数组，这里可以用 any[] 或元组类型对其进行标注
function foo(name: string, age?: number, ...rest: any[]) {}

function bar(name: string, ...rest: [number, boolean]) {}
```

## override

函数重载，TypeScript 中的重载更像是伪重载，它只有一个具体实现，其重载体现在方法调用的签名上而非传统语言（如 C++）具体实现上，

```ts
// 以下面例子为例，函数返回值随 bar 的入参类型而不同
function func(foo: number, bar?: boolean): string | number {
  if (bar) {
    return String(foo);
  } else {
    return foo * 599;
  }
}

// 重载后
function func(foo: number, bar: true): string;
function func(foo: number, bar?: false): number;
function func(foo: number, bar?: boolean): string | number {
  if (bar) {
    return String(foo);
  } else {
    return foo * 599;
  }
}
```

## Async & Generator

异步函数和迭代器的签名，

```ts
// 异步函数的返回值为 Promise 类型
async function asyncFunc(): Promise<void> {}

// 迭代器函数与异步迭代器函数现在基本不使用，当作了解即可
function* generatorFunc(): Iterable<void> {}
async function asyncGeneratorFunc(): AsyncIterable<void> {}
```
