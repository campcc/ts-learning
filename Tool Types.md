# Tool Types

内置工具类型（Builtin Tool Types），

```ts
// 将传入的泛型类型 T 中的所有属性变为可选属性
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// 将传入的泛型类型 T 中的所有属性变为必选属性
type Required<T> = {
  [P in keyof T]-?: T[P];
};

// 将传入的泛型类型 T 中的所有属性变为只读属性
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// 将某个类型 T 映射到一个新的类型，该新类型的键为 K 中的每个属性，值为类型 T
// 1. Record<string, unknown>, Record<string, any> 可以代替 object
// 2. Record<string | number, any>, 键名为字符串或数字，键值类型任意
type Record<K extends keyof any, T> = {
  [P in K]: T;
};

// 键名为字符串的字典类型
type Dictionary<T> = {
  [index: string]: T;
};

// 键名为数字的字典类型
type NumericDictionary<T> = {
  [index: number]: T;
};

// 类型 T 中排除属性 K，生成一个新的类型，相当于求差集
// 与之相反的是求交集，
// type Extract<T, U> = T extends U ? T : never;
type Exclude<T, K> = T extends K ? never : T;

// 接受两个类型参数 T 和 K，返回一个新的类型，新类型中只包含 T 类型中的 K 属性
// 也就是相当于从类型 T 中选取属性 K，生成一个新的类型
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

// 基于 Pick 实现，关键是 Exclude<keyof T, K>，从 T 的键名联合类型剔除 K 的部分，然后作为 Pick 的键名
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

模式匹配工具类型，

```ts
// 定义一个函数类型
type FunctionType = (...args: any) => any;

// 获取函数类型 T 的参数类型，使用 infer 关键字推断出参数类型 P
type Parameters<T extends FunctionType> = T extends (...args: infer P) => any ? P : never;

// 获取函数类型 T 的返回值类型，使用 infer 关键字推断出返回值类型 R
type ReturnType<T extends FunctionType> = T extends (...args: any) => infer R ? R : any;
```

