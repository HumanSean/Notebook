<template>
  <div class="head">
    <!-- 设置按钮 -->
    <h1 class="header">
      <i class="el-icon-edit-outline"></i>
      Notebook
      <el-menu
        mode="horizontal"
        active-text-color="#909399"
        style="display:inline-block;  position: absolute; right: 15px; top: 30px; opacity: 0"
      >
        <el-submenu
          index="1"
          style="width: 20px; height: 20px"
          :show-timeout="100"
        >
          <template slot="title"></template>
          <el-menu-item index="1-1">
            整理模式
            <el-switch
              v-model="sort"
              active-color="#40b883"
              inactive-color="#f2f2f2"
              class="right"
            >
            </el-switch>
          </el-menu-item>
          <el-submenu index="1-2">
            <template slot="title">导出当前笔记</template>
            <el-menu-item index="1-2-1">Markdown格式</el-menu-item>
            <el-menu-item index="1-2-2">word文档</el-menu-item>
            <el-menu-item index="1-2-3">pdf格式</el-menu-item>
          </el-submenu>
          <el-menu-item index="1-3">使用说明</el-menu-item>
        </el-submenu>
      </el-menu>
      <i class="el-icon-setting right"></i>
    </h1>
    <div class="node-tab">
      <div
        v-for="(tab, index) in tabs"
        :key="index"
        :class="{ 'selected-tab': currentTab === tab }"
        @click="
          currentTab = tab;
          $parent.$emit('tab-changed');
        "
      >
        {{ tab }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["sortItem"],
  data() {
    return {
      tabs: ["文件", "大纲"],
      currentTab: "文件",
      sort: false
    };
  },
  watch: {
    sort() {
      this.$emit("update:sortItem", !this.sortItem);
    }
  }
};
</script>

<style scoped>
.header {
  position: relative;
  background: #40b883;
  color: #fff;
  font-weight: 100;
  padding: 7px 13px;
}
.header > i {
  line-height: 2;
}
.right {
  float: right;
  font-size: 0.8em;
  margin-top: 7px;
}
.node-tab {
  position: relative;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 39px;
  padding: 10px 39px 0 39px;
  cursor: pointer;
}
.node-tab div {
  text-align: center;
  padding-bottom: 5px;
}
.selected-tab {
  border-bottom: 3.9px solid #333;
  font-weight: bold;
}
</style>
