<template>
  <div id="app">
    <div class="sidebar">
      <HeadBar :sort-item.sync="sortItem"></HeadBar>
      <Node :sort-item="sortItem" :node="node" v-if="showNode"></Node>
      <Content v-else></Content>
    </div>
    <div class="main">
      <div id="hide" ref="hide">
        <div id="format-tool" contenteditable="false" ref="formatTool">
          <i class="el-icon-caret-bottom" v-if="isHeading"></i>
          <span id="tool-bar">
            <el-popover
              :disabled="showPopover"
              placement="top-start"
              trigger="hover"
              @show="highlight(true)"
              @hide="highlight(false)"
              :close-delay="50"
            >
              <div class="icon">
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    标题1(Alt+1)<br />Markdown: # Space
                  </div>
                  <i
                    @mousedown="execCmd('formatBlock', 'H1')"
                    class="iconfont icon-h1"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    标题2(Alt+2)<br />Markdown: ## Space
                  </div>
                  <i
                    @mousedown="execCmd('formatBlock', 'H2')"
                    class="iconfont icon-h2"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    标题3(Alt+3)<br />Markdown: ### Space
                  </div>
                  <i
                    @mousedown="execCmd('formatBlock', 'H3')"
                    class="iconfont icon-h3"
                  ></i>
                </el-tooltip>
                <span v-if="showMore">
                  <el-tooltip placement="top" :open-delay="500">
                    <div class="tooltip" slot="content">
                      标题4(Alt+4)<br />Markdown: #### Space
                    </div>
                    <i
                      @mousedown="execCmd('formatBlock', 'H4')"
                      class="iconfont icon-h4"
                    ></i>
                  </el-tooltip>
                  <el-tooltip placement="top" :open-delay="500">
                    <div class="tooltip" slot="content">
                      标题5(Alt+5)<br />Markdown: ##### Space
                    </div>
                    <i
                      @mousedown="execCmd('formatBlock', 'H5')"
                      class="iconfont icon-h5"
                    ></i>
                  </el-tooltip>
                  <el-tooltip placement="top" :open-delay="500">
                    <div class="tooltip" slot="content">
                      标题6(Alt+6)<br />Markdown: ###### Space
                    </div>
                    <i
                      @mousedown="execCmd('formatBlock', 'H6')"
                      class="iconfont icon-h6"
                    ></i>
                  </el-tooltip>
                  <el-tooltip placement="top" :open-delay="500">
                    <div class="tooltip" slot="content">
                      正文(Alt+~)
                    </div>
                    <i
                      @mousedown="execCmd('formatBlock', 'P')"
                      class="iconfont icon-p"
                    ></i>
                  </el-tooltip>
                </span>
                <el-tooltip placement="top" :open-delay="500">
                  <div slot="content" class="tooltip">
                    无序列表(Alt+Q)<br />Markdown: - Space
                  </div>
                  <i
                    @mousedown="execCmd('insertUnorderedList')"
                    class="iconfont icon-ol"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    有序列表(Alt+W)<br />Markdown: 1. Space
                  </div>
                  <i
                    @mousedown="execCmd('insertOrderedList')"
                    class="iconfont icon-ul"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    引用(Alt+E)<br />Markdown: > Space
                  </div>
                  <i
                    @mousedown="execCmd('formatBlock', 'BLOCKQUOTE')"
                    class="iconfont icon-quote"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    代码块(Alt+R)<br />Markdown: ` Space
                  </div>
                  <i
                    @mousedown="execCmd('formatBlock', 'pre')"
                    class="iconfont icon-code"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    插入图片(暂不支持上传)
                  </div>
                  <i
                    @mousedown="execCmd('insertImage')"
                    class="iconfont icon-image"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    撤销(Ctrl+Z)
                  </div>
                  <i
                    @mousedown="execCmd('undo')"
                    class="iconfont icon-undo"
                  ></i>
                </el-tooltip>
                <el-tooltip placement="top" :open-delay="500">
                  <div class="tooltip" slot="content">
                    重做(Ctrl+Y)
                  </div>
                  <i
                    @mousedown="execCmd('redo')"
                    class="iconfont icon-redo"
                  ></i>
                </el-tooltip>
                <i
                  class="iconfont icon-more"
                  @mouseover="showMore = true"
                  v-if="!showMore"
                ></i>
              </div>
              <i slot="reference" class="el-icon-circle-plus-outline"></i>
            </el-popover>
          </span>
        </div>
      </div>
      <div id="hide2" ref="hide2">
        <div id="edit-tool" contenteditable="false" ref="editTool">
          <el-tooltip placement="top" :open-delay="500">
            <div slot="content" class="tooltip">
              无序列表(Alt+Q)<br />Markdown: - Space
            </div>
            <i
              @mousedown="execCmd('insertUnorderedList')"
              class="iconfont icon-ol"
            ></i>
          </el-tooltip>
          <el-tooltip placement="top" :open-delay="500">
            <div class="tooltip" slot="content">
              有序列表(Alt+W)<br />Markdown: 1. Space
            </div>
            <i
              @mousedown="execCmd('insertOrderedList')"
              class="iconfont icon-ul"
            ></i>
          </el-tooltip>
          <el-tooltip placement="top" :open-delay="500">
            <div class="tooltip" slot="content">
              加粗(Ctrl+B)
            </div>
            <i @mousedown="execCmd('bold')" class="iconfont icon-bold"></i>
          </el-tooltip>
          <el-tooltip placement="top" :open-delay="500">
            <div class="tooltip" slot="content">
              斜体(Ctrl+I)
            </div>
            <i @mousedown="execCmd('italic')" class="iconfont icon-italic"></i>
          </el-tooltip>
          <el-tooltip placement="top" :open-delay="500">
            <div class="tooltip" slot="content">
              下划线(Ctrl+U)
            </div>
            <i
              @mousedown="execCmd('underline')"
              class="iconfont icon-underline"
            ></i>
          </el-tooltip>
          <el-tooltip placement="top" :open-delay="500">
            <div class="tooltip" slot="content">
              删除线(Ctrl+S)
            </div>
            <i
              @mousedown="execCmd('strikethrough')"
              class="iconfont icon-strikethrough"
            ></i>
          </el-tooltip>
          <el-tooltip placement="top" :open-delay="500">
            <div class="tooltip" slot="content">
              超链接
            </div>
            <i
              @mousedown="execCmd('createLink')"
              class="iconfont icon-link"
            ></i>
          </el-tooltip>
        </div>
      </div>
      <div
        class="edit-area"
        :contenteditable="editMode"
        @mouseup="showEditTool"
        @mouseover="showFormatTool"
        @input="saveNote"
        @keydown.enter="newLine"
        ref="edit"
        v-html="content"
      ></div>
    </div>
  </div>
</template>

<script>
import HeadBar from "@/components/HeadBar.vue";
import Node from "@/components/Node.vue";
import Content from "@/components/Content.vue";
export default {
  name: "Home",
  components: {
    HeadBar,
    Node,
    Content
  },
  data() {
    return {
      node: JSON.parse(localStorage.getItem("allNotes")) || {
        name: "Notebook",
        children: [{ name: "我的笔记本", children: [] }]
      },
      showNode: true,
      sortItem: false,
      editMode: false,
      currentNodeId: 0,
      content: "",
      isHeading: false,
      showPopover: false,
      showMore: false
    };
  },
  mounted() {
    this.$on("tab-changed", () => {
      // 切换文件目录或当前文件大纲
      this.showNode = !this.showNode;
    });
    this.$on("open-note", node => {
      // 打开笔记
      if (this.currentNodeId !== node.id) {
        this.currentNodeId = node.id;
        this.$refs.edit.innerHTML =
          localStorage.getItem(node.id) || "<div><br></div>";
        this.initEditor();
        this.hideFormatTool();
        this.hideEditTool();
      }
    });
    this.$on("delete-note", node => {
      let removeItem = node => {
        if (node.children) {
          // 是笔记本
          node.children.forEach(node => {
            removeItem(node);
          });
        } else {
          // 是笔记
          if (node.id === this.currentNodeId) {
            // 删到了当前打开的笔记
            this.closeEditor();
          }
          localStorage.removeItem(node.id);
        }
      };
      removeItem(node);
    });
  },
  watch: {
    node: {
      // immediate: true,
      deep: true,
      handler(n) {
        localStorage.setItem("allNotes", JSON.stringify(n));
      }
    }
  },
  methods: {
    // 初始化编辑器
    initEditor() {
      this.editMode = true;
      document.execCommand("insertBrOnReturn");
    },
    // 关闭编辑器
    closeEditor() {
      this.currentNodeId = 0;
      this.editMode = false;
      this.$refs.edit.innerHTML = "";
    },
    newLine() {
      // console.log(e.target);
    },
    // 实时保存笔记
    saveNote(e) {
      if (!this.$refs.edit.innerHTML || this.$refs.edit.innerHTML === "<br>") {
        document.execCommand("insertHTML", false, "<div><br></div>");
      }
      // 隐藏tooltip
      this.highlight(false);
      this.hideFormatTool();
      this.hideEditTool();
      localStorage.setItem(this.currentNodeId, e.target.innerHTML);
    },
    showFormatTool(e) {
      // if (
      //   e.target.previousElementSibling &&
      //   e.target.previousElementSibling.id === "format-tool"
      // ) {
      //   console.log("已经正常显示啦");

      //   return;
      // }
      if (!this.$refs.hide2.childElementCount) {
        // console.log(e.target.parentNode.className === "edit-area");
        // eslint-disable-next-line no-undef
        // window.el = e.target;
        // 编辑工具在的时候不显示
        return;
      }
      if (e.target.className === "edit-area" || e.target.id === "edit-tool") {
        // this.hideFormatTool();
        return;
      }
      if (e.target.parentNode.className === "edit-area") {
        // 创建工具条
        this.isHeading = e.target.tagName[0] === "H" ? true : false;
        if (!this.$refs.hide.childElementCount) {
          // 先隐藏上一个
          this.highlight(false);
          this.hideFormatTool();
          this.showMore = false;
        }
        let range = document.createRange();
        let node = e.target.childNodes[0];
        range.setStart(node, 0);
        range.setEnd(node, 0);
        window.getSelection().removeAllRanges();
        window.getSelection().addRange(range);
        console.log("range");
        e.target.before(this.$refs.formatTool);
      }
    },
    showEditTool(e) {
      var findParent = node => {
        if (node.parentNode.className !== "edit-area") {
          console.log(1);

          findParent(node.parentNode);
        } else {
          console.log(2);

          node.before(this.$refs.editTool);
        }
      };
      setTimeout(() => {
        let selection = window.getSelection();
        // 当出现选区时
        if (!selection.isCollapsed) {
          // 此时隐藏格式工具
          this.hideFormatTool();
          if (
            selection.anchorNode.compareDocumentPosition(selection.focusNode) >
            selection.focusNode.compareDocumentPosition(selection.anchorNode)
          ) {
            findParent(selection.anchorNode);
          } else {
            findParent(selection.focusNode);
          }
        } else {
          // 不存在选区时即隐藏，也包括了点击编辑工具的之后
          this.hideEditTool();
          this.showFormatTool(e);
        }
      });
    },
    highlight(btn) {
      if (this.$refs.formatTool.nextElementSibling) {
        this.$refs.formatTool.nextElementSibling.style.background = btn
          ? "#d7ffd7"
          : "transparent";
      }
    },
    hideFormatTool() {
      this.$refs.hide.append(this.$refs.formatTool);
    },
    hideEditTool() {
      this.$refs.hide2.append(this.$refs.editTool);
    },
    execCmd(cmd, val = null) {
      if (cmd === "createLink") {
        val = this.$prompt("请输入超链接");
      }
      if (cmd === "insertImage") {
        val = this.$prompt("请输入图片链接");
      }
      document.execCommand(cmd, false, val);
      this.showPopover = true;
      setTimeout(() => {
        this.showPopover = false;
      }, 300);
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  outline: none;
}
::-webkit-scrollbar {
  display: none;
}
html,
body,
#app {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.sidebar {
  width: 20%;
  min-width: 280px;
  height: 100%;
  background: #f2f2f2;
  overflow: auto;
  font-family: "Segoe Light UI";
  border-right: 1px solid #ccc;
  user-select: none;
}
.main {
  position: absolute;
  right: 0;
  top: 0;
  width: 100%;
  max-width: calc(100% - 280px);
  height: 100%;
}

.edit-area {
  position: relative;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  padding: 52px;
  overflow: scroll;
}

.edit-area * {
  position: relative;
}

.edit-area > * {
  transition: 0;
  padding: 3px;
  border-radius: 3px;
  min-height: 22px;
}

#hide,
#hide2 {
  display: none;
}

#format-tool {
  position: absolute;
  display: inline-block;
  width: 60px;
  white-space: nowrap;
  left: -8px;
  margin-top: -3px;
  /* top: 7px; */
  font-size: 23px;
  overflow: hidden;
  z-index: 1;
  /* background: #f2f2f2; */
  cursor: pointer;
  pointer-events: none;
}

#format-tool > * {
  pointer-events: auto;
  float: right;
  /* padding-right: 3px; */
}
#format-tool > *:first-child {
  margin-right: 5px;
}

.iconfont {
  display: inline-block;
  padding: 2px 5px;
  margin: 0 1px;
  border-radius: 3px;
  cursor: pointer;
  z-index: 2;
}
.iconfont:hover {
  background: #f2f2f2;
}

#edit-tool {
  position: absolute;
  margin-top: -40px;
  background: #fff;
  border-radius: 3px;
  border: solid #f2f2f2 1px;
  animation: appear 0.5s;
  padding: 7px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}
#edit-tool .iconfont {
  padding: 3px 5px;
}
.tooltip {
  text-align: center;
}
.el-popover {
  transform: translateY(10px);
  padding: 10px !important;
}
@keyframes appear {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>
