# Type

类型别名 `type`。

类型别名除了可以声明一个类型外，还可以用作工具类泛型，

```ts
type MaybeNull<T> = T | null;

function process(input: MaybeNull<{ handler: () => {} }>) {
  input?.handler();
}

type MaybeArray<T> = T | T[];

function ensureArray<T>(input: MaybeArray<T>): T[] {
  return Array.isArray(input) ? input : [input];
}
```

## 联合类型与交叉类型

对应按位或（`|`）和按位与（`&`），联合类型只需要符合成员之一即可，而交叉类型需要严格符合每一位成员。

```ts
type StrAndNum = string & number; // never

type UnionIntersection1 = (1 | 2 | 3) & (1 | 2); // 1 | 2
type UnionIntersection2 = (string | number | symbol) & string; // string
```

## 索引类型

包含三个部分：索引签名类型、索引类型查询与索引类型访问。

**索引签名类型**

在接口或类型别名中，快速声明一个键值类型一致的类型结构，

```ts
interface AllStringTypes {
  [key: string]: string;
}

type AllStringTypes = {
  [key: string]: string;
};

// 字符串索引签名类型中仍然可以声明数字类型、Symbol 类型的键
// 原因是 JavaScript 中，对于 obj[prop] 形式的访问会将数字索引访问转换为字符串索引访问
const foo: AllStringTypes = {
  str: "9",
  99: "99",
  [Symbol("999")]: "999",
};
```

**索引类型查询**

通过 `keyof` 操作符可以将对象中的所有键转换为对应字面量类型，然后再组合成联合类型，

```ts
interface Foo {
  name: 1;
  999: 2;
}

type FooKeys = keyof Foo; // "name" | 999

// vscode 中悬浮只能看到 'keyof Foo'，可以使用下面方式
// keyof 相当于 Object.keys(Foo).join("|")
type FooKeys = keyof Foo & {}; // "name" | 999
```

**索引类型访问**

通过类型下标可以访问对应索引类型，

```ts
interface NumberRecord {
  [key: string]: number;
}

type PropType = NumberRecord[string]; // number
```

**映射类型**

```ts
type Clone<T> = {
  [K in keyof T]: T[K];
};
```
