# Structural Type System

## 结构化类型系统

TypeScript 中，两个类型的比较是通过比较这**两个类型上实际拥有的属性与方法**，比如下面的例子，

```ts
class Cat {
  eat() {}
}

class Dog {
  eat() {}
}

function feedCat(cat: Cat) {}

feedCat(new Dog()); // 不会报错
```

这种结构化类型的设计来源于 [鸭子测试（Duck Test）](https://zh.wikipedia.org/wiki/%E9%B8%AD%E5%AD%90%E6%B5%8B%E8%AF%95)，

> 如果它看起来像鸭子、游泳像鸭子、叫声像鸭子，那么它可能就是只鸭子。

结构化类型系统的核心理念就是：**基于类型的结构判断类型的兼容性**。

除了**基于类型结构进行兼容性判断的结构化类型系统**以外，还有一种**基于类型名进行兼容性判断的类型系统**，叫做**标称类型系统（Nominal Typing System）**。

## Nominal Typing System

在标称类型系统中，两个可兼容的类型，其名称必须是完全一致的，比如以下代码在标称系统中就是错误的，

```ts
type USD = number;
type CNY = number;

const CNYCount: CNY = 200;
const USDCount: USD = 200;

function addCNY(source: CNY, input: CNY) {
  return source + input;
}

addCNY(CNYCount, USDCount);
```

但在 TypeScript 中，基于结构化类型判断，USD 与 CNY（分别代表美元单位与人民币单位）被认为是两个完全一致的类型，

我们可以通过类型附带额外信息的方式在 TypeScript 中模拟标称类型系统的行为，

```ts
export declare class TagProtector<T extends string> {
  protected __tag__: T;
}

export type Nominal<T, U extends string> = T & TagProtector<U>;

type CNY = Nominal<number, "CNY">;
type USD = Nominal<number, "USD">;

// ...

declare const tag: unique symbol;

declare type Tagged<Token> = {
  readonly [tag]: Token;
};

export type Opaque<Type, Token = unknown> = Type & Tagged<Token>;
```

上述方式是通过类型层面进行的约束，我们也可以从运行时层面进行模拟，

```ts
class CNY {
  private __tag!: void;
  constructor(public value: number) {}
}
class USD {
  private __tag!: void;
  constructor(public value: number) {}
}

const CNYCount = new CNY(100);
const USDCount = new USD(100);

function addCNY(source: CNY, input: CNY) {
  return source.value + input.value;
}

addCNY(CNYCount, CNYCount);
```

> 小结：TypeScript 的结构化类型系统是基于类型结构进行比较的，而标称类型系统是基于类型名来进行比较的。在 TypeScript 中，可以通过为类型附加信息的方式，从类型层面或者逻辑层面出发去模拟标称类型系统
