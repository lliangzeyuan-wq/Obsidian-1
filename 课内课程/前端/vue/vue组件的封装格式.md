

# 补充示例（.vue 组件完整封装结构）

vue

```
<!-- HTML结构 -->
<template>
  <div class="box">{{ msg }}</div>
</template>

<!-- JS逻辑 -->
<script setup>
import { ref } from 'vue'
const msg = ref('组件内容')
</script>

<!-- scoped局部隔离样式 -->
<style scoped>
.box {
  color: red;
}
</style>
```

这三部分全部封装在一个组件文件里，对应选项 C 的描述。