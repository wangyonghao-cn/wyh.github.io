---
title: npx 命令
---

# 开始

> npm自5.2版本开始，增加了npx 命令，本文主要解释它是怎么运作的

<!--more-->

## 使用

> 若你想使用create-react-app模块创建一个项目my-app，你就可以使用以下命令

```js、jscript、javascript
npx create-react-app my-app
```

> npx会先从你本地node_modules目录下去检索是否有create-react-app命令，没有会再去检索环境变量path
> 若你需要一个全局安装的模块，使用它，它会将包下载到一个临时目录，等使用完之后再删除，避免了全局安装

### npx允许指定版本

```js、jscript、javascript
npx create-react-app@1.0.0 my-app
```

> 如果你不想让npx下载模块，可使用 [--no-install] 参数

```js、jscript、javascript
npx --no-install create-react-app my-app
```

> 同样的，如果你不想让npx使用本地模块，强制使用远程模块，可使用[--ignore-existing] 参数

```js、jscript、javascript
npx --ignore-existing create-react-app my-app
```

## 可指定版本运行

> 利用其下载模块的特性，可以直接调用包其他版本的功能，比如你本机安装的是6.9.0版本的node，执行以下代码

```js、jscript、javascript
npx node@0.12.8 -v
```

> 某些场景下，这种方法比nvm更加方便

## npx参数

> -p 指定安装某个模块，上面安装命令也可写为

```js、jscript、javascript
npx -p node@0.12.8 node -v
```

> 这对于安装多可模块很有帮助

```js、jscript、javascript
npx -p node@0.12.8 -p ajs@1.0.0 node -v
```

> -c会将命令完全交给npx解释执行