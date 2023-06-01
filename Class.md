# Class

类的类型，描述了构造函数、属性、方法和访问符（Accessor）

```ts
class Foo {
  private prop: string;

  constructor(value: string) {
    this.prop = value;
  }

  print(): void {
    console.log(this.prop);
  }

  get propA(): string {
    return this.prop + "A";
  }

  set propA(value) {
    this.prop = value + "A";
  }
}
```

- 属性的类型标注类似于变量
- 构造函数、方法、存取器的类型编标注类似于函数
- setter 不允许进行返回值的类型标注

类也支持通过类表达式的方式创建，

```ts
const Foo = class {
  prop: string;
};
```

## Accessor

TypeScript 支持为类的成员添加如下修饰符，

- public，类，类实例，子类中均能访问
- private，仅能在类的内部访问
- protected，仅能在类与子类中访问
- readonly，前三个都为访问型修饰符，这个是操作型修饰符，设置成员为只读

## static

TypeScript 支持使用 static 标识静态成员，

```ts
class Foo {
  static bar: number = 1;

  public baz: number = 2;
}
```

静态成员只能通过类名 + `.` 的方式访问，其原理是上述代码编译后，静态成员直接被挂载在函数体上，而实例成员挂载在原型上，

```js
var Foo = /** @class */ (function () {
  function Foo() {}
  Foo.bar = 1;
  Foo.prototype.baz = 2;
  return Foo;
})();
```

## extends

基础的角色，**基类（Base）** 与 **派生类（Derived）**，

```ts
class Base {}

class Derived extends Base {}
```

理解继承，就是理解派生类对基类成员的访问和覆盖操作，

- 基类中哪些成员能被派生类访问，由访问修饰符决定
- 派生类可以访问 public 或 protected 修饰符的基类成员
- 可以通过 super 访问到基类中的方法
- 可以通过 override 尝试覆盖基类中的方法

```ts
class Base {
  private foo: string;

  print() {}

  protected say() {}
}

class Derived extends Base {
  print() {
    super.print();
  }

  override print() {}
}
```

## abstract

抽象类是对类结构与方法的抽象，TypeScript 中无法声明静态的抽象成员，

- 一个抽象类描述了一个类中应当有哪些成员（属性、方法等）
- 一个抽象方法描述了这一方法在实际实现中的结构

```ts
abstract class AbsFoo {
  abstract absProp: string;
  abstract get absGetter(): string;
  abstract absMethod(name: string): string;
}
```

实现抽象类，必须完全实现这个抽象类的每一个抽象成员，

```ts
class Foo implements AbsFoo {
  absProp: string = "absProp";

  get absGetter(): string {
    return "absGetter";
  }

  absMethod(name: string): string {
    return name;
  }
}
```

interface 也可以声明一个类结构被用于实现，

```ts
interface FooStruct {
  absProp: string;
  get absGetter(): string;
  absMethod(input: string): string;
}

class Foo implements FooStruct {
  // ...
}
```

此外，还可以使用 **Newable Interface** 来描述一个类的结构，

```ts
class Foo {}

interface FooStruct {
  new(): Foo;
}

declare const NewableFoo: FooStruct;

let foo = new NewableFoo();
```
