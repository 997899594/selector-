<template>
  <div class="wrapper">
    <selector
      ref="selector"
      :value="keyword"
      @input="inputCondition"
      @onChange="selectCondition"
      :options="options"
      :useOptions="useKql"
    >
      <!-- <template #default="{ prop }">
        <li
          v-for="(item, index) in options"
          :key="index"
          :class="{ active: prop.currSelectIndex === index }"
          @click="prop.selectItem(item)"
        >
          <span class="name">{{ item.name }}</span
          ><span>{{ item.desc }}</span>
        </li>
      </template> -->
    </selector>
    <el-switch
      v-model="useKql"
      active-text="KQL"
      inactive-text="Lucene"
    ></el-switch>
    <!-- <el-button type="primary" @click="$emit('search', keyword)">搜索</el-button> -->
  </div>
</template>
<style lang="scss" scoped>
.wrapper {
  display: flex;
  align-items: center;
  min-width: 720px;
  .el-switch {
    margin-right: 16px;
  }
}
</style>
<script>
import Selector from "./components/Selector";
const connectors = [
  {
    name: ":",
    icon: "sdd",
    bgColor: "",
    belong: ["string"],
    type: "connector",
    desc: "等于某一值"
  },
  {
    name: "*",
    icon: "s",
    bgColor: "",
    belong: [": "],
    type: "connector",
    desc: "等于某一值"
  },
  {
    name: ": *",
    icon: "s",
    bgColor: "",
    belong: ["string"],
    type: "connector",
    desc: "等于任意值"
  },
  {
    name: "<=",
    icon: "s",
    bgColor: "",
    belong: ["date"],
    type: "connector",
    desc: "小于等于某一值"
  },
  {
    name: ">=",
    icon: "s",
    bgColor: "",
    belong: ["date"],
    type: "connector",
    desc: "大于等于某一值"
  },
  {
    name: "<",
    icon: "s",
    bgColor: "",
    belong: ["date"],
    type: "connector",
    desc: "小于某一值"
  },
  {
    name: ">",
    icon: "s",
    bgColor: "",
    belong: ["date"],
    type: "connector",
    desc: "大于某一值"
  },
  {
    name: "and",
    icon: "s",
    bgColor: "",
    belong: ["value"],
    type: "connector",
    desc: "需要多个参数都为true"
  },
  {
    name: "or",
    icon: "s",
    bgColor: "",
    belong: ["value"],
    type: "connector",
    desc: "需要存在参数为true"
  }
];
export default {
  components: {
    Selector
  },
  data() {
    return {
      useKql: true,
      keyword: "",
      options: [],
      fields: [
        {
          name: "ss",
          type: "string"
        },
        {
          name: "time",
          type: "date"
        }
      ],
      logicalConnector: {}
    };
  },
  watch: {
    keyword(nv) {
      this.$emit("onChange", nv);
    }
  },
  created() {
    setTimeout(() => {
      this.options = this.fields;
    }, 500);
  },
  methods: {
    // 输入条件
    inputCondition(e) {
      this.keyword = e;
      if (!this.useKql) return;
      if (e.trim() === "") {
        this.options = this.fields;
        return;
      }
      const keywordArr = e.trim().split(" ");
      const lastKeyword = keywordArr[keywordArr.length - 1];
      let options = this.fields.filter(item => item.name === lastKeyword);
      if (options.length === 0) {
        options = connectors.filter(item => item.name === lastKeyword);
      }
      if (options.length === 0) {
        this.options = [];
        return;
      } else {
        this.$refs.selector.showKeywordList();
      }
      const option = options[0];
      this.handleOptions(option);
    },
    // 选择条件事件
    selectCondition(option) {
      this.keyword += `${option.name} `;
      this.handleOptions(option);
    },
    // 处理选择框项目
    handleOptions(option) {
      switch (option.type) {
        case "connector":
          if (
            option.name === "<" ||
            option.name === "<=" ||
            option.name === ">" ||
            option.name === ">="
          ) {
            this.options = [];
          }
          if (option.name === ":") {
            this.options = [];
          }
          if (option.name === ": *") {
            this.options = connectors.filter(item =>
              item.belong.includes("value")
            );
          }
          if (option.name === "or" || option.name === "and") {
            this.options = this.fields;
          }
          break;
        default:
          this.options = connectors.filter(item =>
            item.belong.includes(option.type)
          );
          break;
      }
    }
  }
};
</script>
