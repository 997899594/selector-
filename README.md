# selector
自由控制下拉列表是否展示，支持自定义下拉列表项的样式

demo效果 如下

![image](https://user-images.githubusercontent.com/52400220/115375892-cb555900-a200-11eb-89ac-cbf1a5fc9122.png)

1.可以上下选择下拉列表项，回车直接选择当前选中项，回车默认选择第一项，选择之后input再次聚焦。

3.下拉列表为空时不展示下拉列表。

5.下拉列表项可以任意修改样式

![image](https://user-images.githubusercontent.com/52400220/115375656-96490680-a200-11eb-9b5f-407340c072ef.png)

使用方法 如下

<!--<selector
      ref="selector"
      :value="keyword"
      @input="inputCondition"
      @onChange="selectCondition"
      :options="options"
      :useOptions="useKql"
    >

    <template #default="{ prop }">
        <li
          v-for="(item, index) in options"
          :key="index"
          :class="{ active: prop.currSelectIndex === index }"
          @click="prop.selectItem(item)"
        >
          <span class="name">{{ item.name }}</span
          ><span>{{ item.desc }}</span>
        </li>
     </template>
     
</selector>-->

输入触发inputCondition，选择触发selectCondition，options为下拉列表数组，useOption为false即可关闭下拉功能，注释部分的li标签可以自己写子元素和自定义样式。
ref提供hideKeywordList()和showKeywordList()控制下拉列表是否显示。
