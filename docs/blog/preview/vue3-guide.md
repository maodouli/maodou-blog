---
title: Vue3 入门指南
tags:
  - vue3
  - 前端框架
  - javascript
createTime: 2025/11/16 19:13:00
permalink: /blog/68apz2dc/
---

# Vue 3 入门指南

## Vue 3 简介

Vue 3 是一个渐进式 JavaScript 框架，用于构建用户界面。相比 Vue 2，Vue 3 在性能、TypeScript 支持和组合式 API 方面有显著改进。

### 主要特性

- **组合式 API** - 更好的逻辑复用和组织
- **更好的 TypeScript 支持** - 完整的类型推导
- **性能优化** - 更快的渲染和更小的包体积
- **新的响应式系统** - 基于 Proxy 的响应式

## 环境要求

| 环境 | 最低要求 | 推荐配置 |
|------|---------|---------|
| Node.js | 14.x | 16.x 或更高 |
| 包管理器 | npm 6.x | npm 8.x 或 pnpm/yarn |
| 浏览器 | Chrome 64+ | 现代浏览器 |

## 安装 Vue 3

### 使用 CDN（快速开始）

```html
<!DOCTYPE html>
<html>
<head>
    <title>Vue 3 示例</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
    <div id="app">
        {{ message }}
    </div>

    <script>
        const { createApp } = Vue
        
        createApp({
            data() {
                return {
                    message: 'Hello Vue 3!'
                }
            }
        }).mount('#app')
    </script>
</body>
</html>
```

### 使用 Vue CLI（推荐）

```bash
# 安装 Vue CLI
npm install -g @vue/cli

# 创建项目
vue create my-vue3-app

# 选择 Vue 3 预设
# 进入项目目录
cd my-vue3-app

# 启动开发服务器
npm run serve
```

### 使用 Vite（现代构建工具）

```bash
# 使用 npm
npm create vue@latest my-vue3-app

# 或使用 yarn
yarn create vue my-vue3-app

# 或使用 pnpm
pnpm create vue my-vue3-app

# 进入项目并安装依赖
cd my-vue3-app
npm install

# 启动开发服务器
npm run dev
```

## 项目结构

典型的 Vue 3 项目结构：

```
my-vue3-app/
├── public/                 # 静态资源
│   └── index.html
├── src/
│   ├── assets/            # 项目资源
│   ├── components/        # 组件目录
│   ├── views/             # 页面组件
│   ├── router/            # 路由配置
│   ├── store/             # 状态管理
│   ├── App.vue            # 根组件
│   └── main.js            # 入口文件
├── package.json
└── vite.config.js         # Vite 配置
```

## 组合式 API

### setup() 函数

```vue
<template>
  <div>
    <h1>{{ title }}</h1>
    <p>计数: {{ count }}</p>
    <button @click="increment">+1</button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const title = ref('Vue 3 组合式 API')
    const count = ref(0)
    
    const increment = () => {
      count.value++
    }
    
    return {
      title,
      count,
      increment
    }
  }
}
</script>
```

### 使用 `<script setup>` 语法糖

```vue
<template>
  <div>
    <h1>{{ title }}</h1>
    <p>计数: {{ count }}</p>
    <button @click="increment">+1</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const title = ref('Vue 3 组合式 API')
const count = ref(0)

const increment = () => {
  count.value++
}
</script>
```

## 响应式系统

### ref 和 reactive

```vue
<script setup>
import { ref, reactive } from 'vue'

// 基本类型使用 ref
const count = ref(0)
const message = ref('Hello')

// 对象使用 reactive
const user = reactive({
  name: '张三',
  age: 25,
  email: 'zhangsan@example.com'
})

// 修改响应式数据
count.value = 10
user.name = '李四'
</script>
```

### 计算属性

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('张')
const lastName = ref('三')

// 计算属性
const fullName = computed(() => {
  return firstName.value + lastName.value
})

// 带 setter 的计算属性
const fullNameWithSetter = computed({
  get: () => firstName.value + lastName.value,
  set: (newValue) => {
    const names = newValue.split(' ')
    firstName.value = names[0]
    lastName.value = names[1] || ''
  }
})
</script>
```

### 侦听器

```vue
<script setup>
import { ref, watch, watchEffect } from 'vue'

const count = ref(0)
const message = ref('')

// 侦听单个 ref
watch(count, (newValue, oldValue) => {
  console.log(`计数从 ${oldValue} 变为 ${newValue}`)
})

// 侦听多个源
watch([count, message], ([newCount, newMessage], [oldCount, oldMessage]) => {
  console.log('计数或消息发生变化')
})

// 立即执行的侦听器
watchEffect(() => {
  console.log(`当前计数: ${count.value}`)
})
</script>
```

## 组件通信

### Props 和 Events

```vue
<!-- 父组件 -->
<template>
  <ChildComponent 
    :title="parentTitle" 
    @update-title="handleTitleUpdate"
  />
</template>

<script setup>
import { ref } from 'vue'
import ChildComponent from './ChildComponent.vue'

const parentTitle = ref('父组件标题')

const handleTitleUpdate = (newTitle) => {
  parentTitle.value = newTitle
}
</script>

<!-- 子组件 ChildComponent.vue -->
<template>
  <div>
    <h2>{{ title }}</h2>
    <button @click="updateTitle">更新标题</button>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  title: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['update-title'])

const updateTitle = () => {
  emit('update-title', '新的标题')
}
</script>
```

### Provide/Inject

```vue
<!-- 祖先组件 -->
<script setup>
import { provide, ref } from 'vue'

const theme = ref('dark')

// 提供数据给后代组件
provide('theme', theme)
provide('updateTheme', (newTheme) => {
  theme.value = newTheme
})
</script>

<!-- 后代组件 -->
<script setup>
import { inject } from 'vue'

// 注入祖先组件提供的数据
const theme = inject('theme')
const updateTheme = inject('updateTheme')

const toggleTheme = () => {
  updateTheme(theme.value === 'dark' ? 'light' : 'dark')
}
</script>
```

## 路由配置

### Vue Router 4

```javascript
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'
import About from '../views/About.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

### 在组件中使用路由

```vue
<template>
  <div>
    <nav>
      <router-link to="/">首页</router-link>
      <router-link to="/about">关于</router-link>
    </nav>
    <router-view />
  </div>
</template>

<script setup>
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

// 编程式导航
const goToAbout = () => {
  router.push('/about')
}

// 获取当前路由信息
console.log('当前路径:', route.path)
console.log('查询参数:', route.query)
</script>
```

## 状态管理

### Pinia（推荐）

```javascript
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0
  }),
  
  getters: {
    doubleCount: (state) => state.count * 2
  },
  
  actions: {
    increment() {
      this.count++
    },
    
    async incrementAsync() {
      // 异步操作
      await new Promise(resolve => setTimeout(resolve, 1000))
      this.count++
    }
  }
})
```

### 在组件中使用 Pinia

```vue
<template>
  <div>
    <p>计数: {{ count }}</p>
    <p>双倍计数: {{ doubleCount }}</p>
    <button @click="increment">+1</button>
  </div>
</template>

<script setup>
import { useCounterStore } from '../stores/counter'
import { storeToRefs } from 'pinia'

const counterStore = useCounterStore()

// 使用 storeToRefs 保持响应式
const { count, doubleCount } = storeToRefs(counterStore)

const { increment } = counterStore
</script>
```

## 生命周期

```vue
<script setup>
import { onMounted, onUpdated, onUnmounted } from 'vue'

// 组件挂载后
onMounted(() => {
  console.log('组件已挂载')
  // 可以在这里进行 DOM 操作或数据获取
})

// 组件更新后
onUpdated(() => {
  console.log('组件已更新')
})

// 组件卸载前
onUnmounted(() => {
  console.log('组件即将卸载')
  // 清理工作，如清除定时器、取消订阅等
})
</script>
```

## 最佳实践

### 代码组织

```vue
<script setup>
// 1. 导入依赖
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'

// 2. 响应式数据
const count = ref(0)
const user = ref({ name: '', age: 0 })

// 3. 计算属性
const isAdult = computed(() => user.value.age >= 18)

// 4. 方法
const increment = () => count.value++
const fetchUser = async () => {
  // 异步数据获取
}

// 5. 生命周期
onMounted(() => {
  fetchUser()
})

// 6. 模板引用
const inputRef = ref(null)
</script>
```

### 性能优化

1. **使用 `v-once` 和 `v-memo`**
2. **合理使用计算属性缓存**
3. **避免不必要的响应式**
4. **使用异步组件**

## 下一步学习

1. **深入学习组合式 API**
2. **掌握 TypeScript 集成**
3. **学习 Vue 3 生态工具**
   - Vue Router 4
   - Pinia（状态管理）
   - Vite（构建工具）
4. **实践项目开发**

### 推荐资源

- [Vue 3 官方文档](https://v3.vuejs.org/)
- [Vue Mastery](https://www.vuemastery.com/)
- [Vue.js 中文社区](https://vue-js.com/)

**祝您 Vue 3 学习之旅顺利！**