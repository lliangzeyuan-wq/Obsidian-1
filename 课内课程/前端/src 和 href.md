
# src 和 href 区别、原理、应用场景（Vue/HTML 通用）

## 一、基础核心原理（先分清本质）

### href

**全称 hypertext reference 超文本引用**

作用：**建立资源跳转链接关系**，只是记录资源地址，不会立刻加载资源；只有用户触发跳转 / 请求时才访问资源。

属于**链接属性**，用来定义「目标地址」。

### src

**全称 source 资源源文件**

作用：**嵌入、加载并替换当前标签内容**，浏览器解析到 src 会**立刻同步下载资源**，把资源内容嵌入页面。

属于**资源引入属性**，用来把外部资源塞进当前文档。

---

# 二、href 适用标签 + 场景

## 1. `<a href="地址">` 超链接

场景：页面跳转、外链、锚点

html

预览

```
<a href="https://www.baidu.com">去百度</a>
<a href="#top">回到顶部</a> <!-- 页内锚点 -->
```

原理：点击才跳转，页面初始不会加载百度页面。

## 2. `<link href="xxx.css">` 引入外部样式表

场景：加载全局 CSS、图标字体、网站图标 favicon

html

预览

```
<link rel="stylesheet" href="./style.css">
<link rel="icon" href="favicon.ico">
```

原理：链接样式文件，用于渲染页面样式，不替换 link 标签本身。

## 3. `<area href="">` 图片热区跳转

场景：图片分区域点击跳转，极少用。

## href 总结场景

只要是**跳转、关联外部文件、建立引用关系**，不用立刻嵌入资源，全部用 href。

---

# 三、src 适用标签 + 场景

## 1. `<img src="图片地址">` 图片

场景：展示图片、头像、背景图资源

html

预览

```
<img src="./logo.png" alt="logo">
```

原理：浏览器读到立刻下载图片，图片直接显示在 img 标签位置。

## 2. `<script src="js文件">` JS 脚本

场景：引入外部 JS、Vue、jQuery 等库

html

预览

```
<script src="https://cdn.jsdelivr.net/npm/vue@3/dist/vue.global.js"></script>
```

原理：同步下载并执行 JS 代码，阻塞页面渲染（不加 defer/async 时）。

## 3. `<video src="视频地址">` / `<audio src="音频">`

场景：页面内嵌播放音视频

html

预览

```
<video src="./movie.mp4"></video>
```

## 4. `<iframe src="页面地址">` 内嵌网页

场景：页面内嵌入另一个完整网页

html

预览

```
<iframe src="https://vuejs.org"></iframe>
```

## 5. Vue 组件里的使用

- 图片：`<img src="xxx">`
- script 引入库：`src=""`

## src 总结场景

需要**把外部资源直接嵌入、渲染、加载到当前标签位置**，浏览器自动下载资源，用 src。

---

# 四、关键对比表格

表格

|维度|href|src|
|---|---|---|
|核心作用|建立**链接 / 引用关系**|加载并**嵌入资源**到当前标签|
|加载时机|不会自动加载，触发操作才访问|HTML 解析到标签，**立即下载资源**|
|代表标签|`<a>`、`<link>`|`<img>`、`<script>`、`<video>`、`<iframe>`|
|是否替换标签|不替换当前标签|资源内容填充 / 替换当前标签区域|
|典型场景|页面跳转、引入 CSS、图标|展示图片、执行 JS、播放音视频|

---

# 五、高频易错区分（考试常考）

1. **引入 CSS 用 link + href，不能用 src**
    
    ✅ `<link rel="stylesheet" href="style.css">`
    
    ❌ `<link src="style.css">`
    
2. **图片只能 img + src，不能 href**
    
    ✅ `<img src="a.jpg">`
    
    ❌ `<img href="a.jpg">`
    
3. **跳转文字用 a + href**
    
    ✅ `<a href="/home">首页</a>`
    
4. **引入 JS 脚本用 script + src**
    
    ✅ `<script src="vue.js"></script>`
    

# 六、一句话速记

- **href = 指路（跳转、关联文件）**
- **src = 搬进来（加载图片 / 脚本 / 视频到页面）**