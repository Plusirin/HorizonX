# 项目初始化及组件整合



## 一、 初始化文件

### 1. 删除多余文件

在初始化创建项目时，默认创建了很多子文件（一些组件、样式文件等等），我们先把不需要的项目无关文件删干净，需要我们处理的无用文件都在 `src` 文件夹下：

- 删除 `src/views` 下所有文件
- 删除 `src/stores` 下所有文件
- 删除 `src/components` 下所有文件
- 删除 `src/assets` 下所有文件

### 2. 新增文件：

在 `src/views` 文件夹下新建一个 `HomePage.vue` 文件

```vue
<script setup></script>

<template>
  <div>hello isboyjc, This is toolsdog home page!</div>
</template>

<style scoped></style>
```

### 3. 修改路由文件：

然后修改一下 `router/index.js` 路由文件，把之前删掉页面的路由干掉，加上 `HomePage` 页面的路由：

```js
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'HomePage',
      component: () => import('@/views/HomePage.vue')
    }
  ]
})

export default router
```

### 4. 修改App.vue

修改一下项目根组件 `src/App.vue` 的内容，把无用的删了，只留下面内容即可

```vue
<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <RouterView />
</template>

<style scoped></style>
```

### 5. 修改项目入口文件

有一行 `css` 样式的引入，引入的样式文件我们已经删了，所以，把这行代码删除掉

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'

import App from './App.vue'
import router from './router'

// 删除掉，css文件已经删除过了
// import './assets/main.css'

const app = createApp(App)

app.use(createPinia())
app.use(router)

app.mount('#app')
```



## 二、 组件库集成

### 1. 开源组件库 ArcoVue

#### 安装

```shell
npm install --save-dev @arco-design/web-vue

# or 

pnpm add -D @arco-design/web-vue
```

### 2.  按需加载组件 - Arco

使用 [unplugin-vue-components](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fantfu%2Funplugin-vue-components) 和 [unplugin-auto-import](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fantfu%2Funplugin-auto-import) 这两款 `vite` 插件来开启按需加载及自动导入，插件会自动解析模板中的使用到的组件，并导入组件和对应的样式文件。

#### 安装

```shell
npm i unplugin-vue-components -D
npm i -D unplugin-auto-import

# or

pnpm add -D unplugin-vue-components
pnpm add -D unplugin-auto-import
```

#### 配置

修改 `vite.config.js` 文件中配置使用一下插件

```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ArcoResolver } from 'unplugin-vue-components/resolvers'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      resolvers: [ArcoResolver()]
    }),
    Components({
      resolvers: [
        ArcoResolver({
          sideEffect: true
        })
      ]
    })
  ]

  // ...
})
```

#### 测试

在 `home` 页面分别用 `ArcoVue` 的普通按钮 `AButton` 组件和全局提示 `AMessage` 组件进行测试

```vue
<script setup>
const handleClickMini = () => {
  AMessage.info('hello isboyjc, click mini AButton!')
};
</script>
<template>
  <div>hello isboyjc, This is toolsdog home page!</div>
  <a-space>
    <a-button type="primary" size="mini" @click="handleClickMini">Mini</a-button>
    <a-button type="primary" size="small">Small</a-button>
    <a-button type="primary">Medium</a-button>
    <a-button type="primary" size="large">Large</a-button>
  </a-space>
</template>
<style scoped lang="scss"></style>
```

### 3.  自动加载组件 - 自定义组件

使用 [unplugin-vue-components](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fantfu%2Funplugin-vue-components) 和 [unplugin-auto-import](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fantfu%2Funplugin-auto-import) 这两款 `vite` 插件来开启按需加载及自动导入，插件会自动解析模板中的使用到的组件，并导入组件和对应的样式文件。

#### 配置

 `vite.config.js` 文件中

在 `API` 自动引入插件 `AutoImport` 中我们写了指定要去解析的文件 `include`配置，然后在 `import` 选项中指定了自动引入的包名，并且所有自动引入的 `API` 在被自动引入时会添加记录到根目录的 `./.eslintrc-auto-import.json` 文件中，方便我们查看都自动引入了哪些东西，后面我们使用这几个包的 `API` ，就不需要手动引入了，插件会帮我们在文件解析时自动引入。

```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ArcoResolver } from 'unplugin-vue-components/resolvers'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      // 需要去解析的文件
      include: [
        /\.[tj]sx?$/, // .ts, .tsx, .js, .jsx
        /\.vue$/,
        /\.vue\?vue/, // .vue
        /\.md$/ // .md
      ],
      // imports 指定自动引入的包位置（名）
      imports: ['vue', 'pinia', 'vue-router'],
      // 生成相应的自动导入json文件。
      eslintrc: {
        // 启用
        enabled: true,
        // 生成自动导入json文件位置
        filepath: './.eslintrc-auto-import.json',
        // 全局属性值
        globalsPropValue: true
      },
      resolvers: [ArcoResolver()]
    }),
    Components({
      // imports 指定组件所在目录，默认为 src/components
      dirs: ['src/components/', 'src/view/'],
      // 需要去解析的文件
      include: [/\.vue$/, /\.vue\?vue/, /\.md$/],
      resolvers: [
        ArcoResolver({
          sideEffect: true
        })
      ]
    })
  ]

  // ...
})
```

#### 测试

在 `src/components` 文件夹下新建一个 `HelloWorld.vue` 文件，写上下面内容。

```vue
<script setup>
const name = ref('isboyjc')
</script>
<template>
  <div>hello {{ name }}, this is helloworld components</div>
</template>

<style scoped></style>
```

在 `src/views/HomePage.vue` 文件中使用 `HelloWorld` 组件，不要引入，如下：

```vue
<script setup>
const handleClickMini = () => {
  AMessage.info('hello isboyjc, click mini AButton!')
}
</script>
<template>
  <div>hello isboyjc, This is toolsdog home page!</div>
  <a-space>
    <a-button type="primary" size="mini" @click="handleClickMini">
      Mini
    </a-button>
    <a-button type="primary" size="small">Small</a-button>
    <a-button type="primary">Medium</a-button>
    <a-button type="primary" size="large">Large</a-button>
  </a-space>
	
  <!-- 这里 -->
  <HelloWorld />
</template>
<style scoped lang="scss"></style>
```

### 4. 工具库 VueUse

#### 安装

```shell
npm i @vueuse/core

// or

pnpm add @vueuse/core
```

#### 配置

配置 `VueUse` 的组件和指令自动引入需要两个解析器，还是在 `vite.config.js`配置文件中引入，如下：

```js
import {
  ArcoResolver,
  VueUseComponentsResolver,
  VueUseDirectiveResolver
} from 'unplugin-vue-components/resolvers'
```

在配置文件 `plugins` 模块中之前写过的 `Components` 插件中使用一下这两个解析器就好了：

```js
plugins: [
  Components({
    // ...
    VueUseComponentsResolver(),
    VueUseDirectiveResolver()
  })
]
```

 `API` 方法的自动引入就很简单了，还是配置文件中只需要在之前用过的 `AutoImport` 插件中添加一个 `VueUse` 包名配置就行了：

```js
plugins: [
  AutoImport({
    // 新增 '@vueuse/core'
    imports: ['vue', 'pinia', 'vue-router', '@vueuse/core'],
    resolvers: [ArcoResolver()]
  }),
]
```

### 5. 配置 ESLint

#### 默认配置

根目录下的 `.eslintrc.cjs` 是 `ESLint` 配置，当前默认如下

```js
/* eslint-env node */
require('@rushstack/eslint-patch/modern-module-resolution')

module.exports = {
  root: true,
  'extends': [
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/eslint-config-prettier'
  ],
  parserOptions: {
    ecmaVersion: 'latest'
  }
}
```



根目录下的 `.prettierrc.json` 是 `Prettier` 配置，当前默认如下

```js
{}
```

#### 配置

只需要将它写入 `ESLint` 配置的 `extends` 中让 `Lint` 工具识别下就好了，如下

```js
/* eslint-env node */
require('@rushstack/eslint-patch/modern-module-resolution')

module.exports = {
  root: true,
  'extends': [
    // 这里
    './.eslintrc-auto-import.json',
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/eslint-config-prettier'
  ],
  parserOptions: {
    ecmaVersion: 'latest'
  }
}
```

注意，`extends` 这个继承配置的是一个数组，最终会将所有规则项进行合并，出现冲突的时候，后面的会覆盖前面的，我们在初始化项目安装时默认给加上去了 3 个，我们看看这三个是什么？

- plugin:vue/vue3-essential
  - `ESLint Vue3` 插件扩展
- eslint:recommended
  - `ESLint` 官方扩展
- @vue/eslint-config-prettier
  - `Prettier NPM` 扩展

```js
/* eslint-env node */
require('@rushstack/eslint-patch/modern-module-resolution')

module.exports = {
  root: true,
  'extends': [
    './.eslintrc-auto-import.json',
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/eslint-config-prettier'
  ],
  // 这里
  globals: {
    defineEmits: "readonly",
    defineProps: "readonly",
    defineExpose: "readonly",
    withDefaults: "readonly",
  },
  parserOptions: {
    ecmaVersion: 'latest'
  }
}
```

如上，添加全局属性时，`readonly` 代表只读，`writable` 代表可写，可写就是可以手动覆盖这个全局变量的意思，我们当然是不允许覆盖了，所以全部都设置成了 `readonly`。

其实我们不需要去在开头写这么一行注释指定文件环境，我们给 `ESLint` 指定一下常用环境，即 `env` 属性配置，让 `ESLint` 自己去匹配，我们不写这个配置的话默认它只支持浏览器 `browser` 的规则解析，写上环境配置，我们最终的配置文件如下：

```js
require("@rushstack/eslint-patch/modern-module-resolution");

module.exports = {
  // 这里
  env: {
    // 浏览器环境
    browser: true,
    // Node环境
    node: true,
    // 启用除了modules以外的所有 ECMAScript 6 特性
    es2021: true,
  },
  root: true,
  extends: [
    "./.eslintrc-auto-import.json",
    "plugin:vue/vue3-essential",
    "eslint:recommended",
    "@vue/eslint-config-prettier",
  ],
  globals: {
    defineEmits: "readonly",
    defineProps: "readonly",
    defineExpose: "readonly",
    withDefaults: "readonly",
  },
  parserOptions: {
    ecmaVersion: "latest",
  },
  rules: {
    semi: ["warn", "never"], // 禁止尾部使用分号
    "no-debugger": "warn", // 禁止出现 debugger
  },
};
```



### 6. 配置 Prettier

配置完了 `ESLint` ，我们再来看`Prettier`，我这边配置了几个常用的，如下

```js
{
  "semi": false,
  "singleQuote": true,
  "printWidth": 80,
  "trailingComma": "none",
  "arrowParens": "avoid",
  "tabWidth": 2
}
```

### 7. SVG图标 - SVGIcon

#### 安装

```shell
npm i -D unplugin-icons

# or

pnpm add unplugin-icons -D
```

#### 配置

先在 `src/assets` 文件夹下创建一个 `svg` 文件夹，然后在其下面新建两个文件夹，一个叫 `home` 一个叫 `user`，在下面随便放 2 个 `svg` 文件，我们下面会给大家简单演示怎么配置和使用自定义的 SVG 图标集合。整个的配置如下所示：

```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// API自动引入插件
import AutoImport from "unplugin-auto-import/vite"
// 组件自动引入插件
import Components from "unplugin-vue-components/vite"
import {
  ArcoResolver,
  VueUseComponentsResolver,
  VueUseDirectiveResolver
} from 'unplugin-vue-components/resolvers'
// icon 插件
import Icons from "unplugin-icons/vite"
// icon 自动引入解析器
import IconsResolver from "unplugin-icons/resolver"
// icon 加载 loader
import { FileSystemIconLoader } from "unplugin-icons/loaders"

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      include: [
        /\.[tj]sx?$/,
        /\.vue$/,
        /\.vue\?vue/,
        /\.md$/,
      ],
      imports: ["vue", "pinia", "vue-router"],
      eslintrc: {
        enabled: true,
        filepath: "./.eslintrc-auto-import.json",
        globalsPropValue: true,
      },
      resolvers: [ArcoResolver()],
    }),
    Components({
      dirs: ["src/components/", "src/view/"， "@vueuse/core"],
      include: [/\.vue$/, /\.vue\?vue/, /\.md$/],
      resolvers: [
        ArcoResolver({
          sideEffect: true,
        }),
        VueUseComponentsResolver(),
        VueUseDirectiveResolver(),
	// icon组件自动引入解析器使用
        IconsResolver({
          // icon自动引入的组件前缀 - 为了统一组件icon组件名称格式
          prefix: "icon",
          // 自定义的icon模块集合
          customCollections: ["user", "home"],
        }),
      ],
    }),
    // Icon 插件配置
    Icons({
      compiler: "vue3",
      customCollections: {
        // user图标集，给svg文件设置 fill="currentColor" 属性，使图标的颜色具有适应性
        user: FileSystemIconLoader("src/assets/svg/user", (svg) =>
          svg.replace(/^<svg /, '<svg fill="currentColor" ')
        ),
        // home 模块图标集
        home: FileSystemIconLoader("src/assets/svg/home", (svg) =>
          svg.replace(/^<svg /, '<svg fill="currentColor" ')
        ),
      },
      autoInstall: true,
    }),
  ]
})
```

然后我们在 `HomePage` 页面中直接去按规则写组件就行（我们上面配置了自动引入的 `icon` 组件前缀必须写 `<icon-` ），那我们使用起来如下：

```html
<!-- ep:alarm-clock  改成  ep-alarm-clock -->
<icon-ep-alarm-clock class=""/>
```

那如何使用我们自定义的图标集，比如上面我们配置中自定义的 `home、user` 集合，我们在 `home` 文件夹下放了一个 `copy.svg` 还有一个 `download.svg` 文件，使用如下

```html
<!-- 相当于 home:xxx，xxx就是home文件夹下的图标文件名 -->
<icon-home-copy class=""/>
<icon-home-download class=""/>
```





```vue
<script setup>
const handleClickMini = () => {
  AMessage.info("hello isboyjc, click mini AButton!")
}
</script>
<template>
  <div>hello isboyjc, This is toolsdog home page!</div>
  <a-space>
    <a-button type="primary" size="mini" @click="handleClickMini">
      Mini
    </a-button>
    <a-button type="primary" size="small">Small</a-button>
    <a-button type="primary">Medium</a-button>
    <a-button type="primary" size="large">Large</a-button>
  </a-space>

  <HelloWorld />

  <!-- Icon -->
  <div>
    <icon-ep-alarm-clock class="" />
    <icon-home-copy class="" />
    <icon-home-download class="" />
  </div>
</template>
<style scoped></style>
```

### 8. Styles 公共样式管理、初始化样式

下载 [Normalize.css](https://link.juejin.cn/?target=https%3A%2F%2Fnecolas.github.io%2Fnormalize.css%2Flatest%2Fnormalize.css) 到 `Styles` 文件夹下，当然你也可以直接 `npm` 安装它，不过我比较喜欢直接下载下来这个文件。

下载下来之后直接在 `main.js` 最上面引入一下就行了，如下

```js
import { createApp } from "vue"
import { createPinia } from "pinia"
// 这里
import "@/styles/normalize.css"

import App from "./App.vue"
import router from "./router"

const app = createApp(App)

app.use(createPinia())
app.use(router)

app.mount("#app")
```

### 9. UnoCSS

安装

```shell
npm install --save-dev unocss @unocss/preset-uno @unocss/preset-attributify @unocss/transformer-directives

# or

pnpm i -D unocss @unocss/preset-uno @unocss/preset-attributify @unocss/transformer-directives
```

配置

还是在 `Vite` 插件配置中，也就是 `vite.config.js` 文件中配置

```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// ...

// Unocss 插件
import Unocss from 'unocss/vite'
// Unocss 默认预设
import presetUno from '@unocss/preset-uno'
// Unocss 属性模式预设
import presetAttributify from '@unocss/preset-attributify'
// Unocss 指令转换插件
import transformerDirective from '@unocss/transformer-directives'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    // ...

    // 新增一个 Unocss 插件配置
    Unocss({
      // 预设
      presets: [presetUno(), presetAttributify()],
      // 指令转换插件
      transformers: [transformerDirective()],
      // 自定义规则
      rules: []
    }),
  ]

  // ...
})
```

在使用之前我们先在入口文件 `main.js` 中一下 `UnoCSS` 的 `css` 文件：

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import '@/styles/normalize.css' 

// 导入Unocss样式 
import 'uno.css' 

// ...
```

基础使用如下：

```html
<button class="bg-blue-400 hover:bg-blue-500 text-sm text-white font-mono font-light py-2 px-4 rounded border-2 border-blue-200 dark:bg-blue-500 dark:hover:bg-blue-600">
  Button
</button>
```

如果你对这些原子化样式不太熟悉的话，直接打开样式查询地址 [uno.antfu.me/](https://link.juejin.cn/?target=https%3A%2F%2Funo.antfu.me%2F)，输入对应 `CSS` 样式去搜就可以

我们上面安装了 `@unocss/preset-attributify` 属性预设，所以我们也可以使用属性模式，可以将实用程序分为多个属性，这样写：

```html
<button 
  bg="blue-400 hover:blue-500 dark:blue-500 dark:hover:blue-600"
  text="sm white"
  font="mono light"
  p="y-2 x-4"
  border="2 rounded blue-200"
>
  Button
</button>
```

肯定有人觉得原子化 `CSS` 全写在 `HTML` 中，太多的话，看上去眼花缭乱很不爽，那所以我们还安装了指令转换器插件 `@unocss/transformer-directives` 就是为了解决这个问题了。

它允许我们使用 `@apply`指令在 `style` 中写原子化 `CSS` ：

```html
<button class="btn-style">
  Button
</button>

<style>
.btn-style{
  @apply bg-blue-400 text-sm text-white font-mono font-light py-2 px-4 rounded border-2 border-blue-200;
  @apply hover:bg-blue-500;
  @apply dark:bg-blue-500 dark:hover:bg-blue-600;
}
</style>
```

### 10. Utils、Hooks、API 管理

我们在项目 `src` 目录下添加一个 `utils` 文件夹，此文件夹用于存放我们项目中用到的一些公共方法文件。

同样的，我们在项目 `src` 目录下添加一个 `hooks` 文件夹，此文件夹用于存放我们项目中用到的一些 `hooks` 文件，因为我们用 `Vue3` 的 `CompsitionAPI`，后面用多了自然会有很多 `hooks` 文件，针对一些公用的，我们统一管理在此文件夹下。

平常我们做项目，一般和请求相关的文件都统一放在一个文件夹下，所以我们在项目 `src` 目录下添加一个 `api` 文件夹，用于存放和请求相关的文件，因为项目性质，所以我们应该暂时用不到请求后端接口，那这边 `api` 文件的一些配置以及 `axios` 的封装甚至 `API Mock` 配置这里都先不展开说了，回头我会写好这块代码提交到 `GitHub` ，后面有需要的话会写一篇 `axios` 封装相关的文章来单独介绍这块。

