<template>
  <div class="chart">
    <h1>Bar chart Canvas</h1>

    <div class="chart-scroll">
      <canvas ref="canvasRef"></canvas>
    </div>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from "vue";

const props = defineProps({
  values: {
    type: Array,
    required: true,
  },
});

const canvasRef = ref(null);
const canvasHeight = 300;
const startX = 40;
const barWidth = 35;

const onlyNumbers = computed(() => props.values.filter((item) => (typeof item === "number" && item > 0 ? item : null)));

const endChartValue = computed(() => {
  if (onlyNumbers.value.length === 0) return;

  const maxValue = Math.max(...onlyNumbers.value);

  return Math.round(maxValue * 0.1 + maxValue);
});

const startChartValue = computed(() => {
  if (onlyNumbers.value.length === 0) return;

  const minValue = Math.min(...onlyNumbers.value);

  return Math.round(minValue - minValue * 0.1);
});

const valuesPoints = computed(() => {
  let currentX = startX;
  let currentY = 0;
  const points = [];

  onlyNumbers.value.map((item, idx) => {
    if (idx !== 0) {
      const xCordinate = currentX + barWidth + 20;
      currentX = xCordinate;
    }
    const yCordinate = canvasHeight - ((item - startChartValue.value) / (endChartValue.value - startChartValue.value)) * (canvasHeight - 20);
    currentY = yCordinate;

    const pointsBox = {
      value: item,
      x: currentX,
      y: currentY,
    };

    points.push({ ...pointsBox });
  });

  return points;
});

function getBarHeight(value) {
  const min = startChartValue.value;
  const max = endChartValue.value;

  const result = (value - min) / (max - min); // 43 / 61 = 0.7 * 300 = 210

  return result * canvasHeight;
}

const canvasWidth = computed(() => props.values.length * (barWidth + 20));

const drawCanvas = () => {
  const canvas = canvasRef.value;

  const width = canvasWidth.value;
  const scale = 5;

  canvas.width = width * scale;
  canvas.height = canvasHeight * scale;

  canvas.style.width = `${width}px`;
  canvas.style.height = `${canvasHeight}px`;

  const ctx = canvas.getContext("2d");
  ctx.scale(scale, scale);

  // End line
  ctx.beginPath();
  ctx.moveTo(startX, 10);
  ctx.lineTo(canvasWidth.value, 10);
  ctx.strokeStyle = "#ccc";
  ctx.lineWidth = 1;
  ctx.font = "10";
  ctx.fillText(endChartValue.value, startX - 18, 10);
  ctx.stroke();

  // Start line
  ctx.beginPath();
  ctx.moveTo(startX, canvasHeight - 15);
  ctx.lineTo(canvasWidth.value, canvasHeight - 15);
  ctx.strokeStyle = "#ccc";
  ctx.lineWidth = 1;
  ctx.font = "10";
  ctx.fillText(startChartValue.value, startX - 18, canvasHeight - 0);
  ctx.stroke();

  valuesPoints.value.forEach((point, idx) => {
    ctx.beginPath();
    ctx.fillStyle = "#374151";
    ctx.roundRect(point.x, canvasHeight - getBarHeight(point.value) - 15, barWidth, getBarHeight(point.value), 4);
    ctx.fill();

    ctx.font = "400 12px Arial";
    ctx.fillStyle = "#374140";
    ctx.fillText(point.value, point.x + 12, point.y - 35);

    ctx.font = "400 10px Arial";
    ctx.fillStyle = "#000";
    ctx.fillText(idx + 1, startX + 12 + idx * (barWidth + 20), canvasHeight);
  });
};

onMounted(() => {
  drawCanvas();
});
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
