# Code

Recommended VS Code configs & plugins

## plugins

- [TypeScript Importer](https://marketplace.visualstudio.com/items?itemName=pmneo.tsimporter)，收集你项目内所有的类型定义，支持 `:` 类型提示补全，自动导入类型
- [Move TS](https://marketplace.visualstudio.com/items?itemName=stringham.move-ts)，重构和写 demo 的场景，编辑文件路径目录时自动更新路径 & 导入语句
- [Error Lens](https://marketplace.visualstudio.com/items?itemName=PhilHindle.errorlens)，将 VS Code 底部问题栏的错误下直接显示到代码文件中的对应位置

## Configs

open settings, 搜索 `typescript Inlay Hints`，可以选择开启以下功能

- Function Like Return Types，显示推导得到的函数返回值类型；
- Parameter Names，显示函数入参的名称；
- Parameter Types，显示函数入参的类型；
- Variable Types，显示变量的类型。

## Tools

- [ts-node](https://github.com/TypeStrong/ts-node)，直接执行 ts 文件
- [ts-node-dev](https://github.com/wclr/ts-node-dev)，支持监听文件重新执行，通过 ts-node 和 nodemon 实现

全局安装，

```bash
$ npm i ts-node typescript -g
```

可以考虑配置 alias 到 zsh，

```bash
# .zshrc
# alias ts-node
alias tsn="ts-node"
```

ts-node 常用配置，

- -P, --project，指定 tsconfig 位置，默认会查找项目下的 tsconfig.json 文件
- -T, --transpileOnly，禁用掉执行过程中的类型检查过程
- --swc， 使用 swc 来进行编译提升执行速度
- --emit，执行并输出产物，路径为 .ts-node 文件夹
