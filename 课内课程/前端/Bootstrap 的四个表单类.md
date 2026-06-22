
# Bootstrap4 四个表单类完整讲解 + 代码示例

## 前置引入（所有示例都需要）

html

预览

```
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.2/dist/css/bootstrap.min.css">
```

## A. `.form-control`

### 作用

统一美化单行输入框 `input`、下拉 `select`、文本域 `textarea`，自带圆角、内边距、边框、聚焦高亮样式，标准化表单输入控件外观。

### 代码示例

html

预览

```
<!-- 文本输入框美化 -->
<input type="text" class="form-control" placeholder="请输入用户名">

<!-- 下拉选择框美化 -->
<select class="form-control">
  <option>男</option>
  <option>女</option>
</select>

<!-- 多行文本域美化 -->
<textarea class="form-control" rows="3" placeholder="留言"></textarea>
```

## B. `.form-group`

### 作用

**垂直分组容器**，用来包裹一组「标签 label + 输入框 form-control」，自动添加上下外边距，让表单上下分行、间距整齐，实现垂直表单布局。

### 代码示例

html

预览

```
<div class="form-group">
  <label>账号</label>
  <input class="form-control" placeholder="输入账号">
</div>

<div class="form-group">
  <label>密码</label>
  <input type="password" class="form-control" placeholder="输入密码">
</div>
```

## C. `.form-check`

### 作用

单选框 radio、复选框 checkbox 专用外层容器，用来对齐勾选框和文字标签，统一间距、美化原生丑陋的勾选控件。内部搭配 `form-check-input`（勾选框）、`form-check-label`（文字）使用。

### 代码示例

html

预览

```
<!-- 复选框 -->
<div class="form-check">
  <input class="form-check-input" type="checkbox" id="agree">
  <label class="form-check-label" for="agree">同意用户协议</label>
</div>

<!-- 单选框 -->
<div class="form-check">
  <input class="form-check-input" type="radio" name="sex" id="man">
  <label class="form-check-label" for="man">男生</label>
</div>
<div class="form-check">
  <input class="form-check-input" type="radio" name="sex" id="woman">
  <label class="form-check-label" for="woman">女生</label>
</div>
```

## D. `.form-inline`

### 作用

给 `<form>` 标签添加，将内部所有表单元素**横向一行并排展示**，制作搜索栏、短行内表单（行内表单），适合简短搜索场景。

### 代码示例

html

预览

```
<!-- 行内搜索表单，所有控件同一行 -->
<form class="form-inline">
  <input class="form-control mr-2" placeholder="搜索商品">
  <button class="btn btn-primary">搜索</button>
</form>
```

# 综合完整 Demo（四个类全部融合）

html

预览

```
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.2/dist/css/bootstrap.min.css">
</head>
<body>
  <!-- D. form-inline 行内表单 -->
  <form class="form-inline mb-4">
    <input class="form-control mr-2" placeholder="行内搜索">
    <button class="btn btn-success">查询</button>
  </form>

  <!-- B. form-group 垂直分组 + A. form-control 输入框美化 -->
  <div class="form-group">
    <label>姓名</label>
    <input class="form-control" placeholder="请输入姓名">
  </div>

  <!-- C. form-check 复选框组 -->
  <div class="form-check">
    <input class="form-check-input" type="checkbox" id="read">
    <label class="form-check-label" for="read">我已阅读隐私政策</label>
  </div>
</body>
</html>
```

# 速记区分

1. `.form-control`：修饰**输入框本身**
2. `.form-group`：垂直排版，一组标签 + 输入框的**上下容器**
3. `.form-check`：单选 / 复选框专用**对齐容器**
4. `.form-inline`：给 form 加，实现**所有控件横向并排**