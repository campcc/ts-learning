# Generic Types

如果说 TypeScript 是一门对类型进行编程的语言，那么泛型就是这门语言里的（函数）参数。

## 类型别名中的泛型

```ts
// 类型别名的本质就是一个函数，T 就是它的变量，返回值则是一个包含 T 的联合类型
type Factory<T> = T | number | string;

// Stringify 会一个对象类型的所有属性类型转换为 string 类型
type Stringify<T> = {
  [K in keyof T]: string;
};

// Clone 则会进行类型的完全复制，创建一个与输入类型 T 结构相同的新类型
type Clone<T> = {
  [K in keyof T]: T[K];
};

// 内置工具类型 Partial，将一个对象类型的所有属性变为可选属性
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

## 泛型约束与默认值

默认值，

```ts
type Factory<T = boolean> = T | number | string;

// 声明了泛型默认值，调用时就可以不带任何参数了
const foo: Factory = false;

// 默认泛型可以用下面的伪代码解释
function Factory(typeArg = boolean) {
  return [typeArg, number, string];
}
```

泛型约束，可以使用 `extends` 关键字来约束传入的泛型参数必须符合要求，`A extends B` 意味着 A 是 B 的子类型，具体来说

- 更精确，如字面量类型是对应原始类型的子类型，即 `'lee' extends string`
- 更复杂，如 { name: string } 是 {} 的子类型，因为在 {} 的基础上增加了额外的类型

```ts
type ResStatus<ResCode extends number> = ResCode extends 10000 | 10001 | 10002
  ? "success"
  : "failure";

type Res1 = ResStatus<10000>; // "success"
type Res2 = ResStatus<20000>; // "failure"
type Res3 = ResStatus<"10000">; // Error: Type 'string' does not satisfy the constraint 'number'
```

**对象类型中的泛型**

```ts
interface IRes<TData = unknown> {
  code: number;
  error?: string;
  data: TData;
}

interface IUserProfileRes {
  name: string;
  homepage: string;
  avatar: string;
}

function fetchUserProfile(): Promise<IRes<IUserProfileRes>> {}

type StatusSucceed = boolean;

function handleOperation(): Promise<IRes<StatusSucceed>> {}
```

**函数中的泛型**

考虑这样一个场景，假设我们有这么一个函数，它可以接受多个类型的参数并进行对应处理，比如：

- 对于字符串，返回部分截取；
- 对于数字，返回它的 n 倍；
- 对于对象，修改它的属性并返回。

这种场景，即使联合类型、any、重载等方式也能实现，但使用泛型无疑是最优的选择，

```ts
function handle<T>(input: T): T {}
```

`lodash` 的 `pick` 函数，从一个对象中挑选一些属性，并返回这个仅包含这些属性的对象，这里我们可以考虑两个点，

- 从一个对象中进行挑选，所以第一个泛型参数 T 应该是一个对象

```ts
function pick<T extends object, U extends keyof T>(
  object: T,
  ...props: Array<U>
): Pick<T, U>;
```

```ts
// 交换两个泛型参数
function swap<T, U>([start, end]: [T, U]): [U, T] {
  return [end, start];
}

// handle 不再处理对象类型了，而是处理 string ｜ number 的其中一个类型
function handle<T extends string | number>(input: T): T {}

// 泛型应该被内部的逻辑消费
function handle<T>(payload: T): Promise<[T]> {
  return new Promise<[T]>((res, rej) => {
    res([payload]);
  });
}

// 箭头函数的泛型
const handle = <T>(input: T): T => {};

// tsx 文件中泛型的尖括号可能会造成报错，编译器无法识别这是一个组件还是一个泛型，此时你可以让它长得更像泛型一些
const handle = <T extends any>(input: T): T => {};
```

**内置方法中的泛型**

Promise，

```ts
interface PromiseConstructor {
  resolve<T>(value: T | PromiseLike<T>): Promise<T>;
}

declare var Promise: PromiseConstructor;
```
