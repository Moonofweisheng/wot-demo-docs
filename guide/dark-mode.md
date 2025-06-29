---
title: 暗黑模式
iframe: true
url: https://wot-demo.netlify.app/#/pages/about/index
---

# 暗黑模式

## 什么是暗黑模式

暗黑模式（Dark Mode），也被称为夜间模式或深色模式，是一种使用深色背景和浅色文字的界面显示模式。

### 主要特点

- **护眼体验**: 在光线较暗的环境下减少眼部疲劳
- **节省电量**: 在 OLED 屏幕设备上能够显著节省电池消耗
- **视觉美观**: 提供现代化的视觉体验和专业感

## 使用指南

本章节将介绍在 uni-app 项目中实现h5和微信小程序的暗黑模式功能的方案。你可以结合本文内容并参考 uni-app 官方的 [DarkMode 适配指南](https://uniapp.dcloud.net.cn/tutorial/darkmode.html) 来完成适配。

:::tip 提示
App端的暗黑模式适配，请参考 [App 端暗黑模式适配](https://uniapp.dcloud.net.cn/tutorial/darkmode.html#app-plus)完成，本章节暂不涉及。
:::

## 适配组成

暗黑模式的完整适配包含以下几个核心部分：

- **uni-app 平台配置**: 开启 [DarkMode](https://uniapp.dcloud.net.cn/tutorial/darkmode.html) 官方支持
- **UI 组件适配**: [Wot UI](https://wot-design-uni.cn/component/config-provider.html#%E6%B7%B1%E8%89%B2%E6%A8%A1%E5%BC%8F) 组件库的暗黑模式支持
- **样式系统适配**: [UnoCSS](https://unocss.dev/presets/mini#dark-mode) 暗黑模式工具类

## uni-app 平台配置

uni-app 提供了官方的暗黑模式配置方案，通过 `manifest.json` 和 `theme.json` 实现平台级的主题支持，其指南已经比较详细，本章节将简单介绍一下，具体可以参考官方文档。

> 📖 **详细文档**: [uni-app 暗黑模式适配指南](https://uniapp.dcloud.net.cn/tutorial/darkmode.html#get-theme)

### 配置步骤

1. **在 `manifest.config.ts` 中开启暗黑模式**

```json
// H5 配置
"h5": {
  "darkmode": true,
  "themeLocation": "theme.json"
}

// 微信小程序配置
"mp-weixin": {
  "darkmode": true,
  "themeLocation": "theme.json"
}
```

2. **创建 `theme.json` 主题变量文件**

```json
{
  "light": {
    "navBgColor": "#f8f8f8",
    "navTxtStyle": "black",
    "bgColor": "#ffffff",
    "tabBgColor": "#ffffff",
    "tabSelectedColor": "#0165FF"
  },
  "dark": {
    "navBgColor": "#000000",
    "navTxtStyle": "white",
    "bgColor": "#000000",
    "tabBgColor": "#1a1a1a",
    "tabSelectedColor": "#0165FF"
  }
}
```

3. **在 `pages.config.ts` 中引用主题变量**

```json
{
  "globalStyle": {
    "navigationBarBackgroundColor": "@navBgColor",
    "navigationBarTextStyle": "@navTxtStyle",
    "backgroundColor": "@bgColor"
  },
  "tabBar": {
    "backgroundColor": "@tabBgColor",
    "selectedColor": "@tabSelectedColor"
  }
}
```

### 获取当前主题

```javascript
// 获取系统主题信息
const systemInfo = uni.getSystemInfoSync()
console.log('当前主题:', systemInfo.theme) // 'light' 或 'dark'

// 监听主题变化
uni.onThemeChange((res) => {
  console.log('主题已切换到:', res.theme)
})
```

### CSS 媒体查询适配

```css
/* 默认样式 */
.some-background {
  background: white;
}

/* 暗黑模式样式 */
@media (prefers-color-scheme: dark) {
  .some-background {
    background: #1b1b1b;
  }
}
```

## UI 组件适配 (Wot UI)

[Wot Design Uni](https://wot-design-uni.cn/) 组件库原生支持暗黑模式，通过 `wd-config-provider` 组件可以轻松开启全局暗黑模式支持。

### 全局配置

```vue
<!-- App.vue -->
<template>
  <wd-config-provider :theme="theme">
    <!-- 你的应用内容 -->
  </wd-config-provider>
</template>

<script setup>
import { useTheme } from '@/composables/useTheme'

const { theme } = useTheme()
</script>
```

### 组件级配置

```vue
<!-- 单个页面或组件 -->
<template>
  <wd-config-provider theme="dark">
    <wd-button type="primary">暗黑模式按钮</wd-button>
    <wd-cell title="暗黑模式单元格" />
  </wd-config-provider>
</template>
```

> 📖 **详细文档**: [Wot UI 暗黑模式配置](https://wot-design-uni.cn/component/config-provider.html#%E6%B7%B1%E8%89%B2%E6%A8%A1%E5%BC%8F)


## 样式系统适配 (UnoCSS)

### UnoCSS dark 前缀

如果你使用 [UnoCSS](https://unocss.dev/presets/mini#dark-mode)，可以使用 `dark:` 前缀来实现暗黑模式样式。

```html
<view class="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100">
  内容区域
</view>
```

### 样式适配方案

```html
<!-- 使用 UnoCSS dark 前缀 -->
<view class="bg-white dark:bg-[var(--wot-dark-background)]
           text-gray-800 dark:text-[var(--wot-dark-color)]">
  自动适配暗黑模式的内容
</view>

<!-- 使用主题变量 -->
<view class="bg-[var(--wot-bg-color)] text-[var(--wot-text-color)]">
  使用主题变量的内容
</view>

```

### 最佳实践

```html
<!-- ✅ 推荐：结合官方配置和自定义样式 -->
<view class="bg-white dark:bg-[var(--wot-dark-background2)]">

<!-- ❌ 不推荐：硬编码颜色 -->
<view style="background: #000; color: #fff;">
```

## 移除暗黑模式功能

如果你的项目不需要暗黑模式功能，可以按照以下步骤完全移除相关代码：

### 1. 移除官方配置

```json
// src/manifest.config.ts
{
  "h5": {
    // "darkmode": true,              // 删除这行
    // "themeLocation": "theme.json"  // 删除这行
  },
  "mp-weixin": {
    // "darkmode": true,              // 删除这行
    // "themeLocation": "theme.json"  // 删除这行
  }
}
```

### 2. 删除主题配置文件

```bash
# 删除主题配置文件
rm src/theme.json
```

### 3. 简化主题管理逻辑

```typescript
// src/composables/useTheme.ts - 简化版本
export function useTheme() {
  const currentThemeColor = ref(themeColorOptions[0])

  // 只保留主题色功能，移除明暗模式切换逻辑
  function selectThemeColor(option: ThemeColorOption) {
    currentThemeColor.value = option
  }

  return {
    currentThemeColor: computed(() => currentThemeColor.value),
    themeColorOptions,
    selectThemeColor,
  }
}
```

### 4. 清理样式代码

```css
/* 移除暗黑模式相关样式 */
.container {
  background: white;
  color: black;
  /* 移除：dark:bg-gray-900 dark:text-white */
}

/* 移除媒体查询 */
/*
@media (prefers-color-scheme: dark) {
  .container {
    background: #1a1a1a;
    color: white;
  }
}
*/
```

### 5. 更新组件使用

```vue
<script setup>
// 移除主题相关的响应式数据
// const { theme, isDark } = useTheme() // 删除这行

// 移除主题监听
// watch(theme, (newTheme) => { ... }) // 删除这块
</script>

<template>
  <!-- 移除暗黑模式相关的条件渲染和样式 -->
  <view class="bg-white text-gray-900">
    <!-- 移除：dark:bg-gray-900 dark:text-white -->
    内容区域
  </view>
</template>
```

### 6. 完全移除（可选）

如果要完全移除主题功能：

```bash
# 删除主题相关文件
rm src/composables/useTheme.ts

# 从 pages.json 中移除主题变量引用
# 将 @navBgColor 等变量替换为具体颜色值
```

```json
// pages.json - 替换主题变量
{
  "globalStyle": {
    "navigationBarBackgroundColor": "#ffffff", // 替换 @navBgColor
    "navigationBarTextStyle": "black", // 替换 @navTxtStyle
    "backgroundColor": "#f8f8f8" // 替换 @bgColor
  }
}
```

移除后，你的应用将只使用固定的明亮主题，减少代码复杂度和包体积。

> 📖 **了解更多**: [uni-app 暗黑模式适配指南](https://uniapp.dcloud.net.cn/tutorial/darkmode.html) | [wot-design-uni 主题文档](https://wot-design-uni.cn/guide/theme.html)
