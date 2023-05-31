# Literal & Enum

字面量类型、联合类型和枚举。

## Why Literal & Enum

考虑这样的场景，假设我们有一个 interface 描述了响应的消息结构，

```ts
interface {
  code: number;
  status: string;
  data: any;
}
```

大多数情况下，code 可能是一些固定的值如 1000/1001/5000 等，status 可能是 `success`、`failure`，这样的接口定义就失去了 TypeScript 类型即文档的功能。

## Literal

可以考虑使用联合类型 + 字面量常量改造上述接口，

```ts
interface Res {
  code: 10000 | 10001 | 50000;
  status: "success" | "failure";
  data: any;
}
```

这里的 `success` 就是一个字面量类型（Literal Types），

字面量类型代表着比原始类型更精确的类型，同时也是原始类型的子类型，主要包括

- 字符串字面量类型
- 数字字面量类型
- 布尔字面量类型
- 对象字面量类型

单独使用字面量类型比较少见，字面量类型一般和联合类型一起使用，表达一组字面量类型，

```ts
interface Tmp {
  bool: true | false;
  num: 1 | 2 | 3;
  str: "mon" | "lee";
}

// 也可以使用类型别名来复用一组字面量联合类型
type Code = 1000 | 1001 | 5000;
type Status = "success" | "failure";
```

需要注意的是，**无论是原始类型还是对象类型的字面量类型，它们的本质都是类型而不是值**，这意味着它们被存储在内存中的类型空间而非值空间，在编译时会被擦除。

## Enum

enum 可以将常量约束在一个命名空间下，

```ts
enum Items {
  Foo, // 0
  Bar = 500,
  Baz, // 501
}
```

数字型枚举可以使用延迟求值的枚举值，比如函数，延迟枚举值的后续枚举成员需要显式赋值，

```ts
const delayValue = () => 100 + 200;

enum Items {
  Foo = delayValue(),
  Baz = 500,
  Bar,
}
```

枚举是双向赋值的，上述枚举编译后结果为，

```js
"use strict";
var delayValue = function () {
  return 100 + 200;
};
var Items;
(function (Items) {
  Items[(Items["Foo"] = delayValue())] = "Foo";
  Items[(Items["Baz"] = 500)] = "Baz";
  Items[(Items["Bar"] = 501)] = "Bar";
})(Items || (Items = {}));
```

ts 支持常量枚举，常量枚举与普通枚举的区别为，对于常量枚举，你只能通过枚举成员访问枚举值（而不能通过值访问成员），

```ts
const enum Items {
  Foo,
  Bar,
  Baz,
}

console.log(Items.Foo); // 0
console.log(Items[0]); // Error: A const enum member can only be accessed using a string literal.
```

这里的原因是常量枚举编译后不会声明 Items，比如下面的代码，

```ts
const enum Items {
  Foo,
  Bar,
  Baz,
}

console.log(Items.Foo);
```

编译后的结果为，

```js
console.log(0 /* Items.Foo */);
```
