<template>
  <!-- 组件的结构 -->
  <div>
    <h2>学校：{{ school }}</h2>
    <h2>地址：{{ address }}</h2>
  </div>
</template>

<script>
// 组件交互相关的代码
export default {
  //默认暴露 组件的简写形式
  name: "School", //组件名
  data() {
    return {
      name: "山东大学",
      address: "青岛",
    };
  },
};
</script>

<style>
/* 组件的样式 */
</style>
