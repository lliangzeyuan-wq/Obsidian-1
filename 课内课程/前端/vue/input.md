# input 基础（Vue 配套版，贴合你前面回车案例）

`<input>`：**自闭合表单标签，用来接收用户输入**，核心靠 `type` 决定是什么输入框。

## 一、基础语法

html

预览

```
<input type="类型" 属性="值" @keyup.enter="方法名">
```

无闭合标签。

## 二、最常用 type（必考）

1. **type="text"（默认）** 单行普通输入框

html

预览

```
<input type="text" placeholder="请输入名字">
```

1. **type="password"** 密码框，内容变成黑点隐藏

html

预览

```
<input type="password" placeholder="输入密码">
```

1. **type="radio"** 单选，**同组必须 name 一致**

html

预览

```
<input type="radio" name="sex">男
<input type="radio" name="sex">女
```

1. **type="checkbox"** 多选框

html

预览

```
<input type="checkbox">篮球
```

1. **type="submit"** 提交按钮（form 里自带提交表单默认事件，`.prevent`就是阻止它）
2. **type="button"** 普通按钮，无默认行为，靠 @click 绑定函数

## 三、高频属性（你代码里用到的）

1. **placeholder**：灰色提示文字，输入内容自动消失（你示例里`Type your name here..`）

html

预览

```
<input placeholder="请输入">
```

1. **v-model="变量名"**Vue 专用：双向绑定，输入自动同步 data 数据

html

预览

```
<input v-model="name">
```

1. **maxlength="6"** 限制最多输入 6 个字
2. **autofocus**页面加载自动聚焦光标

## 四、搭配键盘事件（你学的按键修饰符）

- `@keyup`：**按键抬起时触发**

html

预览

```
<!--回车抬起执行showinputvalue-->
<input @keyup.enter="showinputvalue">
```

- `.enter/.space/.tab` 按键修饰符，限定按键才触发函数

## 五、结合你之前 Vue 代码一句话

input 输入内容 → 回车抬起触发 methods 里的函数 → `event.target.value`拿到输入内容 → 赋值给 data 变量 → 页面`{{变量}}`展示文字。

需要我整理精简速记版吗？

快速

帮我写作

编程

图像生成

翻译

更多