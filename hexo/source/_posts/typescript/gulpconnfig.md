---
title: Gulp构建TypeScript工程
---

## 前言

> TypeScript(之后简称为ts)作为javascript(之后简称为js)的超集，越来越受到大家的喜爱，其本身拥有的各种特性也大大减少了各位开发大佬们的失误频率，而且学习成本也不高，js也可以很好地迁移转换为ts工程，这也为其流行奠定了基础，本次我们来学习一下怎么使用gulp搭建ts工程

<!--more-->

### 搭建项目
 
> 首先，我们需要先搭一个简单的项目结构，执行以下命令：

```js
mkdir proj
cd proj
mkdir src
mkdir dist
```

>这样我们的项目结构就基本完成了，src作为我们的存放代码的文件夹，然后在主目录proj下运行npm init和安装各个依赖项,入口文件使用./dist/main.js，这个可以随时在package.json中修改

```js
npm install
npm install -g gulp-cli
npm install --save-dev typescript gulp gulp-typescript
```

> 在安装的同时，我们可以写一个Hello Word程序，在src目录下创建main.ts文件并写入以下代码

```ts
function hello(compiler: string) {
    console.log(`Hello from ${compiler}`);
}
hello("TypeScript");
```

> 在根目录proj下创建一个tsconfig.json文件，配置如下

```json
{
    "files": [
        "src/main.ts"
    ],
    "compilerOptions": {
        "noImplicitAny": true,
        "target": "es5"
    }
}
```

> 在根目录proj下创建一个gulpfile.js文件，配置如下

```js
var gulp = require("gulp");
var ts = require("gulp-typescript");
var tsProject = ts.createProject("tsconfig.json");

gulp.task("default", function () {
    return tsProject.src()
        .pipe(tsProject())
        .js.pipe(gulp.dest("dist"));
});
```
> 测试一下至此为止的步骤是不是都是正确的，先执行gulp，会在dist目录下生产一个main.js文件，然后执行这个文件，会输出我们刚刚写的代码

```js
gulp
node dist/main.js
```
输出

```js
Hello from TypeScript
```
> 如果以上步骤没有问题，接下来就模拟真实的项目，我们的代码有多个文件，并且互有引用导出，在src下新建greet.ts文件，并导出一个SayHello的方法

```ts
export function SayHello(name: string) {
    return `Hello from ${name}`;
}
```
> 修改main.ts的代码，从greet.ts中导入SayHello,并输出

```ts
import { SayHello } from "./greet";
console.log(SayHello('Word'));
```
> 最后将src/greet.ts添加到tsconfig.json中

```json
{
    "files": [
        "src/main.ts",
        "src/greet.ts"
    ],
    "compilerOptions": {
        "noImplicitAny": true,
        "target": "es5"
    }
}
```
> 使用node做一下测试，确认gulp是正常工作的

```js
gulp
node dist/main.js
```
### 搭建页面

> 前一节，我们已经搭好项目，接下来我们要学习将其运行在浏览器中，因此，我们将把所有模块捆绑成一个js文件，所幸，Browserify也是做这个事情的，他们还同时支持CommonJS模块，所以不用改变他们的配置我们就可以移植到浏览器。

> 首先，安装Browserify，tsify和vinyl-source-stream。 tsify是Browserify的一个插件，就像gulp-typescript一样，它能够访问TypeScript编译器。 vinyl-source-stream会将Browserify的输出文件适配成gulp能够解析的格式，它叫做 vinyl

```js
npm install --save-dev browserify tsify vinyl-source-stream
```
> 接下来我们继续在src下新建index.html文件并使用main.ts文件来更新它,showHello调用sayHello函数更改页面上段落的文字。
```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <p id="greeting">Loading ...</p>
    <script src="bundle.js"></script>
</body>
</html>
```
```ts
// main.ts
import { sayHello } from "./greet";

function showHello(divName: string, name: string) {
    const elt = document.getElementById(divName);
    elt.innerText = sayHello(name);
}

showHello("greeting", "TypeScript");

```
> 现在修改gulpfile文件如下：

```js
var gulp = require("gulp");
var browserify = require("browserify");
var source = require('vinyl-source-stream');
var tsify = require("tsify");
var paths = {
    pages: ['src/*.html']
};

gulp.task("copy-html", function () {
    return gulp.src(paths.pages)
        .pipe(gulp.dest("dist"));
});

gulp.task("default", gulp.series(["copy-html"], function () {
    return browserify({
        basedir: '.',
        debug: true,
        entries: ['src/main.ts'],
        cache: {},
        packageCache: {}
    })
    .plugin(tsify)
    .bundle()
    .pipe(source('bundle.js'))
    .pipe(gulp.dest("dist"));
}));
```
> 这里增加了copy-html任务并且把它加作default的依赖项。 这样，当 default执行时，copy-html会被首先执行。 我们还修改了 default任务，让它使用tsify插件调用Browserify，而不是gulp-typescript。 方便的是，两者传递相同的参数对象到TypeScript编译器。

调用bundle后，我们使用source（vinyl-source-stream的别名）把输出文件命名为bundle.js。

测试此页面，运行gulp，然后在浏览器里打开dist/index.html。 你应该能在页面上看到“Hello from TypeScript”

> 接下来我们将给GULP添加更多特性：
Watchify启动Gulp并保持运行状态，当你保存文件时自动编译。 帮你进入到编辑-保存-刷新浏览器的循环中。Babel是个十分灵活的编译器，将ES2015及以上版本的代码转换成ES5和ES3。 你可以添加大量自定义的TypeScript目前不支持的转换器。Uglify帮你压缩代码，将花费更少的时间去下载它们 

```js
npm install --save-dev watchify gulp-util
```
> 在gulpfile.js中修改为：
```js
var gulp = require("gulp");
var browserify = require("browserify");
var source = require('vinyl-source-stream');
var watchify = require("watchify");
var tsify = require("tsify");
var gutil = require("gulp-util");
var paths = {
    pages: ['src/*.html']
};

var watchedBrowserify = watchify(browserify({
    basedir: '.',
    debug: true,
    entries: ['src/main.ts'],
    cache: {},
    packageCache: {}
}).plugin(tsify));

gulp.task("copy-html", function () {
    return gulp.src(paths.pages)
        .pipe(gulp.dest("dist"));
});

function bundle() {
    return watchedBrowserify
        .bundle()
        .pipe(source('bundle.js'))
        .pipe(gulp.dest("dist"));
}

gulp.task("default", gulp.series(["copy-html"], bundle));
watchedBrowserify.on("update", bundle);
watchedBrowserify.on("log", gutil.log);
```
> 现在当你执行gulp，它会启动并保持运行状态。 试着改变 main.ts文件里showHello的代码并保存。 接下来使用uglify进行代码混淆
```js
npm install --save-dev gulp-uglify vinyl-buffer gulp-sourcemaps
```

```js
// gulpfile.js
var gulp = require("gulp");
var browserify = require("browserify");
var source = require('vinyl-source-stream');
var tsify = require("tsify");
var uglify = require('gulp-uglify');
var sourcemaps = require('gulp-sourcemaps');
var buffer = require('vinyl-buffer');
var paths = {
    pages: ['src/*.html']
};

gulp.task("copy-html", function () {
    return gulp.src(paths.pages)
        .pipe(gulp.dest("dist"));
});

gulp.task("default", gulp.series(["copy-html"], function () {
    return browserify({
        basedir: '.',
        debug: true,
        entries: ['src/main.ts'],
        cache: {},
        packageCache: {}
    })
    .plugin(tsify)
    .bundle()
    .pipe(source('bundle.js'))
    .pipe(buffer())
    .pipe(sourcemaps.init({loadMaps: true}))
    .pipe(uglify())
    .pipe(sourcemaps.write('./'))
    .pipe(gulp.dest("dist"));
}));
```
> 接下来配置Babel使其将.ts包含到其处理文件中

```js
npm install --save-dev babelify babel-core babel-preset-es2015 vinyl-buffer gulp-sourcemaps
```
> 继续修改gulpfile.js文件

```js
var gulp = require('gulp');
var browserify = require('browserify');
var source = require('vinyl-source-stream');
var tsify = require('tsify');
var sourcemaps = require('gulp-sourcemaps');
var buffer = require('vinyl-buffer');
var paths = {
    pages: ['src/*.html']
};

gulp.task('copyHtml', function () {
    return gulp.src(paths.pages)
        .pipe(gulp.dest('dist'));
});

gulp.task('default', gulp.series(['copyHtml'], function () {
    return browserify({
        basedir: '.',
        debug: true,
        entries: ['src/main.ts'],
        cache: {},
        packageCache: {}
    })
    .plugin(tsify)
    .transform('babelify', {
        presets: ['es2015'],
        extensions: ['.ts']
    })
    .bundle()
    .pipe(source('bundle.js'))
    .pipe(buffer())
    .pipe(sourcemaps.init({loadMaps: true}))
    .pipe(sourcemaps.write('./'))
    .pipe(gulp.dest('dist'));
}));
```
> 我们需要设置TypeScript目标为ES2015。 Babel稍后会从TypeScript生成的ES2015代码中生成ES5。 修改 tsconfig.json:
```
{
    "files": [
        "src/main.ts"
    ],
    "compilerOptions": {
        "noImplicitAny": true,
        "target": "es2015"
    }
}
```