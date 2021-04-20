<template>
  <div class="select-container" @click.stop="">
    <el-input
      :value="value"
      ref="keywordInput"
      @keydown.native="keywordKeydown"
      @input="
        e => {
          $emit('input', e);
        }
      "
      @focus="
        () => {
          if (options.length > 0 && useOptions) {
            showKeywordListFlag = true;
          }
        }
      "
    >
    </el-input>
    <transition name="fade">
      <ul
        v-show="showKeywordListFlag && options.length > 0"
        class="keyword-list"
      >
        <slot :prop="{ currSelectIndex, selectItem }">
          <li
            v-for="(item, index) in options"
            :key="index"
            :class="{ active: currSelectIndex === index }"
            @click="selectItem(item)"
          >
            <span class="name">{{ item.name }}</span
            ><span>{{ item.desc }}</span>
          </li>
        </slot>
      </ul>
    </transition>
  </div>
</template>

<script>
const body = document.querySelector("body");
export default {
  data() {
    return {
      currSelectIndex: -1,
      showKeywordListFlag: false
    };
  },
  props: {
    // 显示值
    value: String,
    // 是否使用下拉功能
    useOptions: {
      type: Boolean,
      default: true
    },
    // 下拉框数据数组
    options: {
      default() {
        return [];
      },
      type: Array
    }
  },
  watch: {
    options: {
      handler() {
        this.currSelectIndex = -1;
      },
      deep: true
    }
  },
  created() {
    body.addEventListener("click", this.hideKeywordList);
  },
  destroyed() {
    body.removeEventListener("click", this.hideKeywordList);
  },
  methods: {
    selectItem(item) {
      if (!this.useOptions) return;
      this.$refs.keywordInput.focus();
      this.$emit("onChange", item);
    },
    // 键盘输入上下回车
    keywordKeydown(e) {
      if (!this.useOptions) return;
      if (this.options.length === 0) {
        this.currSelectIndex = -1;
        return;
      }
      switch (e.key) {
        case "ArrowUp":
          e.preventDefault();
          this.currSelectIndex > 0
            ? this.currSelectIndex--
            : (this.currSelectIndex = this.options.length - 1);
          break;
        case "ArrowDown":
          this.currSelectIndex < this.options.length - 1
            ? this.currSelectIndex++
            : (this.currSelectIndex = 0);
          break;
        case "Enter":
          if (this.currSelectIndex < 0) {
            this.selectItem(this.options[0]);
          } else if (this.currSelectIndex > this.options.length - 1) {
            this.selectItem(this.options[this.options.length - 1]);
          } else {
            this.selectItem(this.options[this.currSelectIndex]);
          }
          break;
        default:
          break;
      }
    },
    hideKeywordList() {
      this.showKeywordListFlag = false;
    },
    showKeywordList() {
      this.showKeywordListFlag = true;
    }
  }
};
</script>

<style lang="scss" scoped>
.select-container {
  flex: 1;
  margin-right: 16px;
  position: relative;
  >>> .el-input__inner {
    border-bottom-width: 3px !important;
  }
  ul.keyword-list {
    position: absolute;
    z-index: 9;
    border: 1px solid #007AFF;
    width: 100%;
    top: 36px;
    height: 275px;
    overflow: auto;
    background: #fff;
    li {
      &:hover,
      &.active {
        background: #fafbfd;
      }
      padding: 0 8px;
      height: 32px;
      display: flex;
      align-items: center;
      span.name {
        width: 250px;
      }
    }
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: height 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  height: 0;
}
</style>
