<script>
import "./index.css";
import LineChartSvg from "./components/charts/LineChartSvg.vue";
import BarChartSvg from "./components/charts/BarChartSvg.vue";
import LineChartCanvas from "./components/charts/LineChartCanvas.vue";
import BarChartCanvas from "./components/charts/BarChartCanvas.vue";

export default {
  components: {
    LineChartSvg,
    LineChartCanvas,
    BarChartSvg,
    BarChartCanvas,
  },

  data() {
    return {
      values: [70, 30, 50, 40, 60, 35, 80, {}, 35, 60, 35, 40, "sdwdw", "100", 60, 35],
    };
  },
};
</script>

<template>
  <h1>Bar and Chart with SVG tag</h1>
  <div class="box">
    <LineChartSvg :values="values" />
    <BarChartSvg :values="values" />
    <LineChartCanvas :values="values" />
    <BarChartCanvas :values="values" />
  </div>
</template>

<style scoped>
.box {
  width: 100%;
}

h1 {
  font-size: 32px;
  font-family: inherit;
  text-align: center;
  padding-bottom: 5px;
  border-bottom: 1px solid #ccc;
}
</style>
