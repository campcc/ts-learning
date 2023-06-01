# Typeof

TypeScript 中存在两种 `typeof`，

- 代码中的 `typeof` 和 JavaScript 一致
- 类型标注、类型别名中的 `typeof` 返回的是一个类型查询

```ts
const str = "string";

type Str = typeof str; // "string"

const func = (input: string) => {
  return input.length > 10;
};

type FuncReturnType = ReturnType<typeof func>; // boolean
```

## 类型守卫
