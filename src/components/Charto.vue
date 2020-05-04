<template>
  <figure>
    <p>{{ type }}</p>
    <h3>{{ name }}</h3>
    <canvas :id="id"></canvas>
  </figure>
</template>
<script>
import Chart from "chart.js";

// Default Chart config
Chart.defaults.global.defaultFontColor = "#fff";
Chart.defaults.global.elements.line.borderWidth = 1;
Chart.defaults.global.elements.rectangle.borderWidth = 1;
Chart.defaults.scale.gridLines.color = "#444";
Chart.defaults.scale.ticks.display = false;

export default {
  name: "charto",
  props: {
    type: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true
    },
    id: {
      type: String,
      required: true
    },
    data: {
      type: Object,
      default: () => {}
    }
  },
  methods: {
    createChart(chartId, chartData) {
      new Chart(chartId, chartData);
    }
  },
  mounted() {
    this.createChart(this.id, this.data);
  }
};
</script>
<style scoped lang="scss">
h3 {
  margin-bottom: 29px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  padding-bottom: 15px;
  font-weight: 200;
}
figure {
  background-color: rgba(255, 255, 255, 0.05);
  padding: 10px 10px 30px;
  p {
    margin-bottom: 7px;
    font-size: 2em;
  }
}
canvas {
  width: 100%;
}
</style>
