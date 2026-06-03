# 1、v-on

**作用：绑定【事件】（点击、鼠标移入等用户操作）**

语法：`v-on:事件名="函数名"`，简写 `@事件名="函数"`

html

预览

```
v-on:click.once="buttonclickedonce"
```

- `v-on:click` → 给按钮绑定**点击事件**
- `.once` 修饰符：事件只执行 1 次
- 点击按钮，自动执行 methods 里对应的函数、修改 data 数据

# 2、v-bind

**作用：绑定【属性 / 样式】，把 JS 数据渲染给标签属性**

语法：`v-bind:属性名="data里的变量"`，简写 `:属性名="变量"`

html

预览

```
v-bind:style="styleobj"
```

- `v-bind:style`：把`data.styleobj`里面的 css 样式，赋值给按钮
- styleobj 在 js 里是样式对象，页面按钮自动套用颜色、边距

## 一句话区分

- **v-on：监听用户动作（事件）** → 干什么（点击触发函数）
- **v-bind：给标签赋值（属性 / 样式）** → 长什么样（样式、src、class）

### 补充简写

- `v-on:click` = `@click`
- `v-bind:style` = `:style`