<template>
  <div class="chart">
    <h1>Bar chart Canvas</h1>

    <div class="chart-scroll">
      <canvas ref="canvasRef"></canvas>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    values: {
      type: Array,
      required: true,
    },
  },

  data() {
    return {
      canvasHeight: 300,
      startX: 40,
      barWidth: 35,
    };
  },

  computed: {
    canvasWidth() {
      return this.values.length * (this.barWidth + 20);
    },

    onlyNumbers() {
      return this.values.filter((item) => (typeof item === "number" && item > 0 ? item : null));
    },

    endChartValue() {
      if (this.onlyNumbers.length === 0) return;

      const maxValue = Math.max(...this.onlyNumbers);

      return Math.round(maxValue * 0.1 + maxValue);
    },

    startChartValue() {
      if (this.onlyNumbers.length === 0) return;

      const minValue = Math.min(...this.onlyNumbers);

      return Math.round(minValue - minValue * 0.1);
    },

    valuesPoints() {
      let currentX = this.startX;
      let currentY = 0;
      const points = [];

      this.onlyNumbers.map((item, idx) => {
        if (idx !== 0) {
          const xCordinate = currentX + this.barWidth + 20;
          currentX = xCordinate;
        }
        const yCordinate = this.canvasHeight - ((item - this.startChartValue) / (this.endChartValue - this.startChartValue)) * (this.canvasHeight - 20);
        currentY = yCordinate;

        const pointsBox = {
          value: item,
          x: currentX,
          y: currentY,
        };

        points.push({ ...pointsBox });
      });

      return points;
    },
  },

  methods: {
    getBarHeight(value) {
      const min = this.startChartValue;
      const max = this.endChartValue;

      const result = (value - min) / (max - min); // 43 / 61 = 0.7 * 300 = 210

      return result * this.canvasHeight;
    },

    drawCanvas() {
      const canvas = this.$refs.canvasRef;

      const width = this.canvasWidth;
      const scale = 5;

      canvas.width = width * scale;
      canvas.height = this.canvasHeight * scale;

      canvas.style.width = `${width}px`;
      canvas.style.height = `${this.canvasHeight}px`;

      const ctx = canvas.getContext("2d");
      ctx.scale(scale, scale);

      // End line
      ctx.beginPath();
      ctx.moveTo(this.startX, 10);
      ctx.lineTo(this.canvasWidth, 10);
      ctx.strokeStyle = "#ccc";
      ctx.lineWidth = 1;
      ctx.font = "10";
      ctx.fillText(this.endChartValue, this.startX - 18, 10);
      ctx.stroke();

      // Start line
      ctx.beginPath();
      ctx.moveTo(this.startX, this.canvasHeight - 15);
      ctx.lineTo(this.canvasWidth, this.canvasHeight - 15);
      ctx.strokeStyle = "#ccc";
      ctx.lineWidth = 1;
      ctx.font = "10";
      ctx.fillText(this.startChartValue, this.startX - 18, this.canvasHeight - 0);
      ctx.stroke();

      this.valuesPoints.forEach((point, idx) => {
        ctx.beginPath();
        ctx.fillStyle = "#374151";
        ctx.roundRect(point.x, this.canvasHeight - this.getBarHeight(point.value) - 15, this.barWidth, this.getBarHeight(point.value), 4);
        ctx.fill();

        ctx.font = "400 12px Arial";
        ctx.fillStyle = "#374140";
        ctx.fillText(point.value, point.x + 12, point.y - 35);

        ctx.font = "400 10px Arial";
        ctx.fillStyle = "#000";
        ctx.fillText(idx + 1, this.startX + 12 + idx * (this.barWidth + 20), this.canvasHeight);
      });
    },
  },

  mounted() {
    this.drawCanvas();
  },
};
</script>

<style scoped>
.chart {
  /* width: 100%; */
  box-sizing: border-box;
  max-width: 100%;
  min-width: 0;
  padding: 20px 24px;
  margin: 16px;
  overflow: hidden;
  background: #ffffff;
  color: #374151;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  box-shadow: 0 4px 16px rgba(15, 23, 42, 0.08);
}

.chart-scroll {
  overflow-x: auto;
  width: 100%;
  max-width: 100%;
  overflow-x: auto;
  overflow-y: hidden;
}
</style>
