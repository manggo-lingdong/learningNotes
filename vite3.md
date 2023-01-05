# vite学习

## 一、开始

### 1、总览

Vite是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，它基于 [原生 ES 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 提供了 [丰富的内建功能](https://cn.vitejs.dev/guide/features.html)，如速度快到惊人的 [模块热更新（HMR）](https://cn.vitejs.dev/guide/features.html#hot-module-replacement)。
- 一套构建指令，它使用 [Rollup](https://rollupjs.org/) 打包你的代码，并且它是预配置的，可输出用于生产环境的高度优化过的静态资源。

### 2、浏览器支持

默认的构建目标是能支持 [原生 ESM 语法的 script 标签](https://caniuse.com/es6-module)、[原生 ESM 动态导入](https://caniuse.com/es6-module-dynamic-import) 和 [`import.meta`](https://caniuse.com/mdn-javascript_operators_import_meta) 的浏览器。传统浏览器可以通过官方插件 [@vitejs/plugin-legacy](https://github.com/vitejs/vite/tree/main/packages/plugin-legacy) 支持 —— 查看 [构建生产版本](https://cn.vitejs.dev/guide/build.html) 章节获取更多细节。

### 3、在线运行vite

你可以通过 [StackBlitz](https://vite.new/) 在线试用 vite。它直接在浏览器中运行基于 Vite 的构建，因此它与本地开发几乎无差别，同时无需在你的机器上安装任何东西。你可以浏览 `vite.new/{template}` 来选择你要使用的框架。

目前支持的模板预设如下：

|             JavaScript              |                TypeScript                 |
| :---------------------------------: | :---------------------------------------: |
| [vanilla](https://vite.new/vanilla) | [vanilla-ts](https://vite.new/vanilla-ts) |
|     [vue](https://vite.new/vue)     |     [vue-ts](https://vite.new/vue-ts)     |
|   [react](https://vite.new/react)   |   [react-ts](https://vite.new/react-ts)   |
|  [preact](https://vite.new/preact)  |  [preact-ts](https://vite.new/preact-ts)  |
|     [lit](https://vite.new/lit)     |     [lit-ts](https://vite.new/lit-ts)     |
|  [svelte](https://vite.new/svelte)  |  [svelte-ts](https://vite.new/svelte-ts)  |

## 二、功能

### 1、NPM 依赖解析和预构建

原生 ES 导入不支持下面这样的裸模块导入：

```js
import { someMethod } from 'my-dep'
```

上面的代码会在浏览器中抛出一个错误。Vite 将会检测到所有被加载的源文件中的此类裸模块导入，并执行以下操作:

1. [预构建](https://cn.vitejs.dev/guide/dep-pre-bundling.html) 它们可以提高页面加载速度，并将 CommonJS / UMD 转换为 ESM 格式。预构建这一步由 [esbuild](http://esbuild.github.io/) 执行，这使得 Vite 的冷启动时间比任何基于 JavaScript 的打包器都要快得多。
2. 重写导入为合法的 URL，例如 `/node_modules/.vite/deps/my-dep.js?v=f3sf2ebd` 以便浏览器能够正确导入它们。

**依赖是强缓存的**

Vite 通过 HTTP 头来缓存请求得到的依赖，所以如果你想要编辑或调试一个依赖，请按照 [这里](https://cn.vitejs.dev/guide/dep-pre-bundling.html#浏览器缓存) 的步骤操作。

### 2、模块热替换

Vite 提供了一套原生 ESM 的 [HMR API](https://cn.vitejs.dev/guide/api-hmr.html)。 具有 HMR 功能的框架可以利用该 API 提供即时、准确的更新，而无需重新加载页面或清除应用程序状态。Vite 内置了 HMR 到 [Vue 单文件组件（SFC）](https://github.com/vitejs/vite/tree/main/packages/plugin-vue) 和 [React Fast Refresh](https://github.com/vitejs/vite/tree/main/packages/plugin-react) 中。也通过 [@prefresh/vite](https://github.com/JoviDeCroock/prefresh/tree/main/packages/vite) 对 Preact 实现了官方集成。

注意，你不需要手动设置这些 —— 当你通过 [`create-vite`](https://cn.vitejs.dev/guide/) 创建应用程序时，所选模板已经为你预先配置了这些。

### 3、TypeScript

Vite 天然支持引入 `.ts` 文件。

Vite 仅执行 `.ts` 文件的转译工作，并 **不** 执行任何类型检查。并假设类型检查已经被你的 IDE 或构建过程接管了（你可以在构建脚本中运行 `tsc --noEmit` 或者安装 `vue-tsc` 然后运行 `vue-tsc --noEmit` 来对你的 `*.vue` 文件做类型检查）。

Vite 使用 [esbuild](https://github.com/evanw/esbuild) 将 TypeScript 转译到 JavaScript，约是 `tsc` 速度的 20~30 倍，同时 HMR 更新反映到浏览器的时间小于 50ms。

使用 [仅含类型的导入和导出](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#type-only-imports-and-export) 形式的语法可以避免潜在的 “仅含类型的导入被不正确打包” 的问题，写法示例如下：



```
import type { T } from 'only/types'
export type { T }
```

#### 3.1 TypeScript 编译器选项

`tsconfig.json` 中 `compilerOptions` 下的一些配置项需要特别注意。

#### `isolatedModules`[¶](https://cn.vitejs.dev/guide/features.html#isolatedmodules)

应该设置为 `true`。

这是因为 `esbuild` 只执行没有类型信息的转译，它并不支持某些特性，如 `const enum` 和隐式类型导入。

你必须在 `tsconfig.json` 中的 `compilerOptions` 下设置 `"isolatedModules": true`。如此做，TS 会警告你不要使用隔离（isolated）转译的功能。

然而，一些库（如：[`vue`](https://github.com/vuejs/core/issues/1228)）不能很好地与 `"isolatedModules": true` 共同工作。你可以在上游仓库修复好之前暂时使用 `"skipLibCheck": true` 来缓解这个错误。

#### `useDefineForClassFields`[¶](https://cn.vitejs.dev/guide/features.html#usedefineforclassfields)

从 Vite v2.5.0 开始，如果 TypeScript 的 target 是 `ESNext`，此选项默认值则为 `true`。这与 [`tsc` v4.3.2 及以后版本的行为](https://github.com/microsoft/TypeScript/pull/42663) 一致。这也是标准的 ECMAScript 的运行时行为。

但对于那些习惯其他编程语言或旧版本 TypeScript 的开发者来说，这可能是违反直觉的。 你可以参阅 [TypeScript 3.7 发布日志](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#the-usedefineforclassfields-flag-and-the-declare-property-modifier) 中了解更多关于如何兼容的信息。

如果你正在使用一个严重依赖 class fields 的库，请注意该库对此选项的预期设置。

大多数库都希望 `"useDefineForClassFields": true`，如 [MobX](https://mobx.js.org/installation.html#use-spec-compliant-transpilation-for-class-properties)，[Vue Class Components 8.x](https://github.com/vuejs/vue-class-component/issues/465) 等。

但是有几个库还没有兼容这个新的默认值，其中包括 [`lit-element`](https://github.com/lit/lit-element/issues/1030)。如果遇到这种情况，请将 `useDefineForClassFields` 设置为 `false`。

##### 影响构建结果的其他编译器选项

- [`extends`](https://www.typescriptlang.org/tsconfig#extends)
- [`importsNotUsedAsValues`](https://www.typescriptlang.org/tsconfig#importsNotUsedAsValues)
- [`preserveValueImports`](https://www.typescriptlang.org/tsconfig#preserveValueImports)
- [`jsxFactory`](https://www.typescriptlang.org/tsconfig#jsxFactory)
- [`jsxFragmentFactory`](https://www.typescriptlang.org/tsconfig#jsxFragmentFactory)

如果你的代码库很难迁移到 `"isolatedModules": true`，或许你可以尝试通过第三方插件来解决，比如 [rollup-plugin-friendly-type-imports](https://www.npmjs.com/package/rollup-plugin-friendly-type-imports)。但是，这种方式不被 Vite 官方支持。

#### 3.2 客户端类型

Vite 默认的类型定义是写给它的 Node.js API 的。要将其补充到一个 Vite 应用的客户端代码环境中，请添加一个 `d.ts` 声明文件：

```
/// <reference types="vite/client" />
```

同时，你也可以将 `vite/client` 添加到 `tsconfig` 中的 `compilerOptions.types` 下：

```
{
  "compilerOptions": {
    "types": ["vite/client"]
  }
}
```

这将会提供以下类型定义补充：

- 资源导入 (例如：导入一个 `.svg` 文件)
- `import.meta.env` 上 Vite 注入的环境变量的类型定义
- `import.meta.hot` 上的 [HMR API](https://cn.vitejs.dev/guide/api-hmr.html) 类型定义

> TIP
>
> 要覆盖默认的类型定义，请在三斜线注释前添加定义。例如，要为 React 组件中的 `*.svg` 文件定义类型：
>
> ```js
> declare module '*.svg' {
>   const content: React.FC<React.SVGProps<SVGElement>>
>   export default content
> }
> 
> /// <reference types="vite/client" />
> ```
>
> 

### 4、Vue

Vite 为 Vue 提供第一优先级支持：

- Vue 3 单文件组件支持：[@vitejs/plugin-vue](https://github.com/vitejs/vite/tree/main/packages/plugin-vue)
- Vue 3 JSX 支持：[@vitejs/plugin-vue-jsx](https://github.com/vitejs/vite/tree/main/packages/plugin-vue-jsx)
- Vue 2.7 支持：[vitejs/vite-plugin-vue2](https://github.com/vitejs/vite-plugin-vue2)
- Vue <2.7 的支持：[underfin/vite-plugin-vue2](https://github.com/underfin/vite-plugin-vue2)

### 5、JSX

`.jsx` 和 `.tsx` 文件同样开箱即用。JSX 的转译同样是通过 [esbuild](https://esbuild.github.io/)。

Vue 用户应使用官方提供的 [@vitejs/plugin-vue-jsx](https://github.com/vitejs/vite/tree/main/packages/plugin-vue-jsx) 插件，它提供了 Vue 3 特性的支持，包括 HMR，全局组件解析，指令和插槽。

如果不是在 React 或 Vue 中使用 JSX，自定义的 `jsxFactory` 和 `jsxFragment` 可以使用 [`esbuild` 选项](https://cn.vitejs.dev/config/shared-options.html#esbuild) 进行配置。例如对 Preact：

```
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  esbuild: {
    jsxFactory: 'h',
    jsxFragment: 'Fragment'
  }
})
```

更多细节详见 [esbuild 文档](https://esbuild.github.io/content-types/#jsx).

你可以使用 `jsxInject`（这是一个仅在 Vite 中使用的选项）为 JSX 注入 helper，以避免手动导入：

```
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  esbuild: {
    jsxInject: `import React from 'react'`
  }
})
```

### 6、CSS

导入 `.css` 文件将会把内容插入到 `<style>` 标签中，同时也带有 HMR 支持。也能够以字符串的形式检索处理后的、作为其模块默认导出的 CSS。

#### 6.1 `@import` 内联和变基

Vite 通过 `postcss-import` 预配置支持了 CSS `@import` 内联，Vite 的路径别名也遵从 CSS `@import`。换句话说，所有 CSS `url()` 引用，即使导入的文件在不同的目录中，也总是自动变基，以确保正确性。

Sass 和 Less 文件也支持 `@import` 别名和 URL 变基（具体请参阅 [CSS Pre-processors](https://cn.vitejs.dev/guide/features.html#css-pre-processors)）。

#### 6.2 PostCSS

如果项目包含有效的 PostCSS 配置 (任何受 [postcss-load-config](https://github.com/postcss/postcss-load-config) 支持的格式，例如 `postcss.config.js`)，它将会自动应用于所有已导入的 CSS。

#### 6.3 CSS Modules

任何以 `.module.css` 为后缀名的 CSS 文件都被认为是一个 [CSS modules 文件](https://github.com/css-modules/css-modules)。导入这样的文件会返回一个相应的模块对象：

```
/* example.module.css */
.red {
  color: red;
}
```

```
import classes from './example.module.css'
document.getElementById('foo').className = classes.red
```

CSS modules 行为可以通过 [`css.modules` 选项](https://cn.vitejs.dev/config/shared-options.html#css-modules) 进行配置。

如果 `css.modules.localsConvention` 设置开启了 camelCase 格式变量名转换（例如 `localsConvention: 'camelCaseOnly'`），你还可以使用按名导入。

```
// .apply-color -> applyColor
import { applyColor } from './example.module.css'
document.getElementById('foo').className = applyColor
```

#### 6.4  CSS 预处理器

由于 Vite 的目标仅为现代浏览器，因此建议使用原生 CSS 变量和实现 CSSWG 草案的 PostCSS 插件（例如 [postcss-nesting](https://github.com/csstools/postcss-plugins/tree/main/plugins/postcss-nesting)）来编写简单的、符合未来标准的 CSS。

话虽如此，但 Vite 也同时提供了对 `.scss`, `.sass`, `.less`, `.styl` 和 `.stylus` 文件的内置支持。没有必要为它们安装特定的 Vite 插件，但必须安装相应的预处理器依赖：

```
# .scss and .sass
npm add -D sass

# .less
npm add -D less

# .styl and .stylus
npm add -D stylus
```

如果使用的是单文件组件，可以通过 `<style lang="sass">`（或其他预处理器）自动开启。

Vite 为 Sass 和 Less 改进了 `@import` 解析，以保证 Vite 别名也能被使用。另外，`url()` 中的相对路径引用的，与根文件不同目录中的 Sass/Less 文件会自动变基以保证正确性。

由于 Stylus API 限制，`@import` 别名和 URL 变基不支持 Stylus。

你还可以通过在文件扩展名前加上 `.module` 来结合使用 CSS modules 和预处理器，例如 `style.module.scss`。

#### 6.5 禁用 CSS 注入页面

自动注入 CSS 内容的行为可以通过 `?inline` 参数来关闭。在关闭时，被处理过的 CSS 字符串将会作为该模块的默认导出，但样式并没有被注入到页面中。

```
import styles from './foo.css' // 样式将会注入页面
import otherStyles from './bar.css?inline' // 样式不会注入页面
```

### 7、静态资源处理

导入一个静态资源会返回解析后的 URL：

```
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
```

添加一些特殊的查询参数可以更改资源被引入的方式：

```
// 显式加载资源为一个 URL
import assetAsURL from './asset.js?url'
```

```
// 以字符串形式加载资源
import assetAsString from './shader.glsl?raw'
```

```
// 加载为 Web Worker
import Worker from './worker.js?worker'
```

```
// 在构建时 Web Worker 内联为 base64 字符串
import InlineWorker from './worker.js?worker&inline'
```

更多细节请见 [静态资源处理](https://cn.vitejs.dev/guide/assets.html)。

### 8、JSON

JSON 可以被直接导入 —— 同样支持具名导入：

```
// 导入整个对象
import json from './example.json'
// 对一个根字段使用具名导入 —— 有效帮助 treeshaking！
import { field } from './example.json'
```

### 9、Glob 导入

Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块：

```
const modules = import.meta.glob('./dir/*.js')
```

以上将会被转译为下面的样子：

```
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js'),
  './dir/bar.js': () => import('./dir/bar.js')
}
```

你可以遍历 `modules` 对象的 key 值来访问相应的模块：

```
for (const path in modules) {
  modules[path]().then((mod) => {
    console.log(path, mod)
  })
}
```

匹配到的文件默认是懒加载的，通过动态导入实现，并会在构建时分离为独立的 chunk。如果你倾向于直接引入所有的模块（例如依赖于这些模块中的副作用首先被应用），你可以传入 `{ eager: true }` 作为第二个参数：

```
const modules = import.meta.glob('./dir/*.js', { eager: true })
```

以上会被转译为下面的样子：

```
// vite 生成的代码
import * as __glob__0_0 from './dir/foo.js'
import * as __glob__0_1 from './dir/bar.js'
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1
}
```

#### 9.1 Glob 导入形式

`import.meta.glob` 都支持以字符串形式导入文件，类似于 [以字符串形式导入资源](https://vitejs.dev/guide/assets.html#importing-asset-as-string)。在这里，我们使用了 [Import Reflection](https://github.com/tc39/proposal-import-reflection) 语法对导入进行断言：

```
const modules = import.meta.glob('./dir/*.js', { as: 'raw' })
```

上面的代码会被转换为下面这样：

```
// code produced by vite（代码由 vite 输出）
const modules = {
  './dir/foo.js': 'export default "foo"\n',
  './dir/bar.js': 'export default "bar"\n'
}
```

`{ as: 'url' }` 还支持将资源作为 URL 加载。

#### 9.2 多个匹配模式

第一个参数可以是一个 glob 数组，例如：

```
const modules = import.meta.glob(['./dir/*.js', './another/*.js'])
```

#### 9.3 反面匹配模式

同样也支持反面 glob 匹配模式（以 `!` 作为前缀）。若要忽略结果中的一些文件，你可以添加“排除匹配模式”作为第一个参数：

```
const modules = import.meta.glob(['./dir/*.js', '!**/bar.js'])
```

```
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js')
}
```

##### 具名导入

也可能你只想要导入模块中的部分内容，那么可以利用 `import` 选项。

```
const modules = import.meta.glob('./dir/*.js', { import: 'setup' })
```

```
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js').then((m) => m.setup),
  './dir/bar.js': () => import('./dir/bar.js').then((m) => m.setup)
}
```

当与 `eager` 一同存在时，甚至可以对这些模块进行 tree-shaking。

```
const modules = import.meta.glob('./dir/*.js', { import: 'setup', eager: true })
```

```
// vite 生成的代码
import { setup as __glob__0_0 } from './dir/foo.js'
import { setup as __glob__0_1 } from './dir/bar.js'
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1
}
```

设置 `import` 为 `default` 可以加载默认导出。

```
const modules = import.meta.glob('./dir/*.js', {
  import: 'default',
  eager: true
})
```

```
// vite 生成的代码
import __glob__0_0 from './dir/foo.js'
import __glob__0_1 from './dir/bar.js'
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1
}
```

##### 自定义查询

你也可以使用 `query` 选项来提供对导入的自定义查询，以供其他插件使用。



```
const modules = import.meta.glob('./dir/*.js', {
  query: { foo: 'bar', bar: true }
})
```



```
// vite 生成的代码
const modules = {
  './dir/foo.js': () =>
    import('./dir/foo.js?foo=bar&bar=true').then((m) => m.setup),
  './dir/bar.js': () =>
    import('./dir/bar.js?foo=bar&bar=true').then((m) => m.setup)
}
```

#### 9.4 Glob 导入注意事项

请注意：

- 这只是一个 Vite 独有的功能而不是一个 Web 或 ES 标准
- 该 Glob 模式会被当成导入标识符：必须是相对路径（以 `./` 开头）或绝对路径（以 `/` 开头，相对于项目根目录解析）或一个别名路径（请看 [`resolve.alias` 选项](https://cn.vitejs.dev/config/shared-options.html#resolve-alias))。
- Glob 匹配是使用 [fast-glob](https://github.com/mrmlnc/fast-glob) 来实现的 —— 阅读它的文档来查阅 [支持的 Glob 模式](https://github.com/mrmlnc/fast-glob#pattern-syntax)。
- 你还需注意，所有 `import.meta.glob` 的参数都必须以字面量传入。你 **不** 可以在其中使用变量或表达式。

### 10、动态导入

和 [glob 导入](https://cn.vitejs.dev/guide/features.html#glob-import) 类似，Vite 也支持带变量的动态导入。

```
const module = await import(`./dir/${file}.js`)
```

注意变量仅代表一层深的文件名。如果 `file` 是 `foo/bar`，导入将会失败。对于更进阶的使用详情，你可以使用 [glob 导入](https://cn.vitejs.dev/guide/features.html#glob-import) 功能。

### 11、WebAssembly

预编译的 `.wasm` 文件可以通过 `?init` 来导入。默认导出一个初始化函数，返回值为所导出 wasm 实例对象的 Promise：

```
import init from './example.wasm?init'

init().then((instance) => {
  instance.exports.test()
})
```

`init` 函数还可以将传递给 `WebAssembly.instantiate` 的导入对象作为其第二个参数：

```
init({
  imports: {
    someFunc: () => {
      /* ... */
    }
  }
}).then(() => {
  /* ... */
})
```

在生产构建当中，体积小于 `assetInlineLimit` 的 `.wasm` 文件将会被内联为 base64 字符串。否则，它们将作为资源复制到 `dist` 目录中，并按需获取。

WARNING

[对 WebAssembly 的 ES 模块集成提案](https://github.com/WebAssembly/esm-integration) 尚未支持。 请使用 [`vite-plugin-wasm`](https://github.com/Menci/vite-plugin-wasm) 或其他社区上的插件来处理。

### 12、Web Workers

#### 12.1 通过构造器导入

一个 Web Worker 可以使用 [`new Worker()`](https://developer.mozilla.org/en-US/docs/Web/API/Worker/Worker) 和 [`new SharedWorker()`](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorker/SharedWorker) 导入。与 worker 后缀相比，这种语法更接近于标准，是创建 worker 的 **推荐** 方式。

```
const worker = new Worker(new URL('./worker.js', import.meta.url))
```

worker 构造函数会接受可以用来创建 “模块” worker 的选项：

```
const worker = new Worker(new URL('./worker.js', import.meta.url), {
  type: 'module'
})
```

#### 12.2 带有查询后缀的导入

你可以在导入请求上添加 `?worker` 或 `?sharedworker` 查询参数来直接导入一个 web worker 脚本。默认导出会是一个自定义 worker 的构造函数：

```
import MyWorker from './worker?worker'

const worker = new MyWorker()
```

Worker 脚本也可以使用 `import` 语句来替代 `importScripts()` —— 注意，在开发过程中，这依赖于浏览器原生支持，目前只在 Chrome 中适用，而在生产版本中，它已经被编译掉了。

默认情况下，worker 脚本将在生产构建中编译成单独的 chunk。如果你想将 worker 内联为 base64 字符串，请添加 `inline` 查询参数：

```
import MyWorker from './worker?worker&inline'
```

如果你想要以一个 URL 的形式读取该 worker，请添加 `url` 这个 query：

```
import MyWorker from './worker?worker&url'
```

关于如何配置打包全部 worker，可以查看 [Worker 选项](https://cn.vitejs.dev/config/worker-options.html) 了解更多相关细节。

### 13、构建优化

> 下面所罗列的功能会自动应用为构建过程的一部分，除非你想禁用它们，否则没有必要显式配置。

#### 13.1 CSS 代码分割

Vite 会自动地将一个异步 chunk 模块中使用到的 CSS 代码抽取出来并为其生成一个单独的文件。这个 CSS 文件将在该异步 chunk 加载完成时自动通过一个 `<link>` 标签载入，该异步 chunk 会保证只在 CSS 加载完毕后再执行，避免发生 [FOUC](https://en.wikipedia.org/wiki/Flash_of_unstyled_content#:~:text=A flash of unstyled content,before all information is retrieved.) 。

如果你更倾向于将所有的 CSS 抽取到一个文件中，你可以通过设置 [`build.cssCodeSplit`](https://cn.vitejs.dev/config/build-options.html#build-csscodesplit) 为 `false` 来禁用 CSS 代码分割。

#### 13.2预加载指令生成

Vite 会为入口 chunk 和它们在打包出的 HTML 中的直接引入自动生成 `<link rel="modulepreload">` 指令。

#### 13.3 异步 Chunk 加载优化

在实际项目中，Rollup 通常会生成 “共用” chunk —— 被两个或以上的其他 chunk 共享的 chunk。与动态导入相结合，会很容易出现下面这种场景：

Entryasync chunk Acommon chunk Casync chunk Bdynamic importdirect import

在无优化的情境下，当异步 chunk `A` 被导入时，浏览器将必须请求和解析 `A`，然后它才能弄清楚它也需要共用 chunk `C`。这会导致额外的网络往返：

```
Entry ---> A ---> C
```

Vite 将使用一个预加载步骤自动重写代码，来分割动态导入调用，以实现当 `A` 被请求时，`C` 也将 **同时** 被请求：

```
Entry ---> (A + C)
```

`C` 也可能有更深的导入，在未优化的场景中，这会导致更多的网络往返。Vite 的优化会跟踪所有的直接导入，无论导入的深度如何，都能够完全消除不必要的往返。