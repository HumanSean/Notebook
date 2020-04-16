<template>
  <div :class="{ 'node-tree': !level }">
    <el-tooltip
      effect="light"
      placement="right"
      :disabled="!$root.$children[0].sortItem || !showTooltip"
    >
      <div slot="content">
        <!-- 编辑名称 -->
        <i
          :class="!editName ? 'el-icon-edit-outline' : 'el-icon-finished'"
          class="right"
          @click="editName = !editName"
        ></i>

        <!-- 向内 -->
        <i
          class="el-icon-arrow-right right"
          @click="moveIn"
          v-if="rightButton"
        ></i>
        <!-- 向外 -->
        <i
          class="el-icon-arrow-left right"
          @click="moveOut"
          v-if="leftButton"
        ></i>
        <!-- 上移 -->
        <i class="el-icon-arrow-up right" @click="moveUp" v-if="upButton"></i>
        <!-- 下移 -->
        <i
          class="el-icon-arrow-down right"
          @click="moveDown"
          v-if="downButton"
        ></i>
        <!-- 删除 -->
        <i class="el-icon-delete right" @click="deleteNode"></i>
      </div>
      <!-- 笔记或文件夹 -->
      <p
        class="node-item"
        @mouseenter="selected = true"
        @mouseleave="selected = false"
        @click.stop="open(node)"
        :class="{
          selected: selected,
          opened: $root.$children[0].currentNodeId === node.id
        }"
        v-if="level > 0"
      >
        <!-- 名称 -->
        <span
          :style="{
            display: 'inline-block',
            'padding-left': indent * 5 + 'px'
          }"
          v-if="!editName"
        >
          <i class="el-icon-folder" v-if="node.children"></i>
          <i class="el-icon-document" v-else></i>
          {{ node.name }}
        </span>
        <input
          :style="{
            display: 'inline-block',
            'margin-left': indent * 5 + 'px',
            fontSize: '1em',
            position: 'absolute',
            width: 233 - indent * 5 + 'px'
          }"
          v-else
          v-model="node.name"
          maxlength="10"
          @keydown.enter="editName = !editName"
        />
        <!-- 指示箭头 -->
        <i
          class="right small"
          :class="showChild ? 'el-icon-arrow-down' : 'el-icon-arrow-left'"
          v-if="node.children && node.children[0]"
        ></i>
        <!-- 新建笔记 -->
        <i
          class="el-icon-document-add right small"
          @click.stop="makeChild"
          v-if="
            (node.children && selected && showChild) ||
              (node.children && !node.children[0] && selected)
          "
        ></i>
        <!-- 新建文件夹 -->
        <i
          class="el-icon-folder-add right small"
          @click.stop="makeFolder"
          v-if="
            (node.children && level < 3 && selected && showChild) ||
              (node.children && !node.children[0] && selected && level < 3)
          "
        ></i>
      </p>
    </el-tooltip>

    <!-- 递归组件 -->
    <div v-if="showChild">
      <Node
        v-for="(n, key) in node.children"
        :key="key"
        :node="n"
        :level="level + 1"
      ></Node>
    </div>
  </div>
</template>

<script>
export default {
  name: "Node",
  props: {
    node: {
      type: Object,
      required: true
    },
    level: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      selected: false,
      showChild: this.level ? false : true,
      editName: false,
      showTooltip: true
    };
  },
  computed: {
    indent() {
      return this.level * 2;
    },
    upButton() {
      return this.$parent.node.children.indexOf(this.node) > 0;
    },
    downButton() {
      return (
        this.$parent.node.children.indexOf(this.node) <
        this.$parent.node.children.length - 1
      );
    },
    leftButton() {
      return this.$parent.level;
    },
    rightButton() {
      let num;
      if (this.node.children) {
        num = 0;
      } else {
        num = 1;
      }
      let index = this.$parent.node.children.indexOf(this.node) - 1;
      if (index < 0) {
        return false;
      }
      return this.level < 3 + num && this.$parent.node.children[index].children;
    }
  },
  methods: {
    open(node) {
      if (node.children) {
        this.showChild = !this.showChild;
      } else {
        this.$root.$children[0].$emit("open-note", node);
      }
    },
    makeChild() {
      // 新建笔记
      this.$prompt("请输入笔记标题", "新建笔记", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        inputPlaceholder: "新建笔记",
        inputPattern: /^.{0,10}$/,
        inputErrorMessage: "请输入十个字以内"
      })
        .then(({ value }) => {
          if (!value) {
            value = "新建笔记";
          }
          this.$message({
            type: "success",
            message: value + "创建成功"
          });
          let index = this.node.children.findIndex(item => !item.children);
          this.node.children.splice(index, 0, {
            name: value,
            id: +new Date()
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消"
          });
        });
    },
    makeFolder() {
      // 新建文件夹
      this.$prompt("请输入文件夹标题", "新建文件夹", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        inputPlaceholder: "新建文件夹",
        inputPattern: /^.{0,10}$/,
        inputErrorMessage: "请输入十个字以内"
      })
        .then(({ value }) => {
          if (!value) {
            value = "新建文件夹";
          }
          this.$message({
            type: "success",
            message: value + "创建成功"
          });
          let newFolder = {
            name: value,
            children: []
          };
          this.node.children.splice(0, 0, newFolder);
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消"
          });
        });
    },
    moveUp() {
      this.showTooltip = false;
      let index = this.$parent.node.children.indexOf(this.node);
      let item = this.$parent.node.children.splice(index, 1)[0];
      this.$parent.node.children.splice(index - 1, 0, item);
    },
    moveDown() {
      this.showTooltip = false;
      let index = this.$parent.node.children.indexOf(this.node);
      let item = this.$parent.node.children.splice(index, 1)[0];
      this.$parent.node.children.splice(index + 1, 0, item);
    },
    moveOut() {
      this.showTooltip = false;
      let index = this.$parent.node.children.indexOf(this.node);
      let item = this.$parent.node.children.splice(index, 1)[0];
      if (item.children) {
        // 是文件夹
        let num = this.$parent.$parent.node.children.findIndex(
          item => !item.children
        );
        this.$parent.$parent.node.children.splice(num, 0, item);
      } else {
        this.$parent.$parent.node.children.push(item);
      }
    },
    moveIn() {
      this.showTooltip = false;
      let index = this.$parent.node.children.indexOf(this.node);
      let item = this.$parent.node.children.splice(index, 1)[0];
      if (item.children) {
        this.$parent.node.children[index - 1].children.unshift(item);
      } else {
        this.$parent.node.children[index - 1].children.push(item);
      }
    },
    deleteNode() {
      if (
        this.$parent.node === this.$root.$children[0].node &&
        this.$parent.node.children.length === 1
      ) {
        // 只有一个根笔记本的情况，阻止删除行为
        this.$message({
          message: "只剩最后一个根文件夹啦，请手下留情┭┮﹏┭┮",
          type: "warning"
        });
        return;
      }
      this.showTooltip = false;
      if (this.node.children && this.node.children[0]) {
        this.$confirm("该文件夹里的笔记将全部删除，是否继续?", "小心！", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
          .then(() => {
            // 删除笔记本
            let index = this.$parent.node.children.indexOf(this.node);
            this.$root.$children[0].$emit("delete-note", this.node);
            this.$parent.node.children.splice(index, 1);
          })
          .catch(() => {});
      } else {
        // 删除笔记
        let index = this.$parent.node.children.indexOf(this.node);
        this.$root.$children[0].$emit("delete-note", this.node);
        this.$parent.node.children.splice(index, 1);
      }
    }
  },
  watch: {
    showTooltip() {
      setTimeout(() => {
        this.showTooltip = true;
      }, 250);
    }
  }
};
</script>

<style scoped>
.node-item {
  position: relative;
  width: 100%;
  height: 48px;
  box-sizing: border-box;
  padding: 10px;
  padding-top: 13px;
  cursor: pointer;
}
.node-item span {
  display: inline-block;
  white-space: nowrap;
  max-width: 80%;
  overflow-x: scroll;
}
.right {
  font-size: 1.5em;
  float: right;
  margin-left: 5px;
  cursor: pointer;
}
.small {
  font-size: 1.3em;
}
.selected {
  background: #eee;
}
.opened {
  background: #40b883;
  color: #fff;
}
</style>
