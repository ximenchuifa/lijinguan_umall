<template>
  <div>
    <h1>首页管理</h1>
    <div class="box" id="main"></div>
  </div>
</template>
<script>
import echarts from "echarts";
import { mapGetters, mapActions } from "vuex";
export default {
  props: [],
  components: {},
  data() {
    return {};
  },
  computed: {
    ...mapGetters({
      list: "cate/list",
    }),
  },
  watch: {
    list: {
      handler() {
        if (this.list.length > 0) {
          // 基于准备好的dom，初始化echarts实例
          var myChart = echarts.init(document.getElementById("main"));
          // 绘制图表
         var option = {
            title: {
              text: "商品分类",
            },
            tooltip: {},
            legend: {
              data: ["下级分类数量"],
            },
            xAxis: {
              data: this.list.map(item=>item.catename),
            },
            yAxis: {},
            series: [
              {
                name: "下级分类数量",
                type: "line",
                data:  this.list.map(item=>item.children.length),
              },
            ],
          };
           // 使用刚指定的配置项和数据显示图表。
          myChart.setOption(option);
        }
      },
      deep:true
    },
  },
  methods: {
    ...mapActions({
      reqList: "cate/reqListAction",
    }),
  },
  mounted() {
    this.reqList();
  },
};
</script>
<style scoped>
.box {
  width: 80%;
  height: 400px;
  margin: 20px auto;
  border: 1px solid #ccc;
}
</style>