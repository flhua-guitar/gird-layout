<template>
  <div class="hello">
    <div id="c1"></div>
  </div>
</template>

<script>
// import VueGridLayout from "vue-grid-layout";
import { Chart } from "@antv/g2";

export default {
  name: "HelloWorld",
  props: {
    msg: Object,
  },
  data() {
    return {
      data: [
        { genre: "Sports", sold: 275 },
        { genre: "Strategy", sold: 115 },
        { genre: "Action", sold: 120 },
        { genre: "Shooter", sold: 350 },
        { genre: "Other", sold: 150 },
      ],
    };
  },

  components: {},
  created() {},
  mounted() {
    this.initG2();
  },
  methods: {
    // forceFit() {
    //   chart.forceFit();
    // },
    initG2() {
      this.chart = null;
      // Step 1: 创建 Chart 对象
      this.chart = new Chart({
        container: "c1", // 指定图表容器 ID
        width: this.msg.width, // 指定图表宽度
        height: this.msg.height, // 指定图表高度
        // autoFit: true,
      });

      // Step 2: 载入数据源
      this.chart.data(this.data);

      // Step 3: 创建图形语法，绘制柱状图
      this.chart.interval().position("genre*sold");

      // Step 4: 渲染图表
      this.chart.render();
    },
    changeG2() {
      this.chart.changeSize(this.msg.width, this.msg.height - 30);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
