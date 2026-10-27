<template>
  <div class="chart">
    <h1>Line chart Canvas</h1>

    <div class="chart-scroll" @mousemove="onMove" @mouseleave="onLeave">
      <canvas class="canvas" ref="canvasRef"></canvas>

      <!-- Hover line and Tooltip -->
      <div
        v-if="isHovering"
        class="hover-line"
        :style="{
          transform: `translateX(${targetX}px)`,
          height: `${canvasHeight - 25}px`,
        }"
      ></div>

      <div
        v-if="isHovering"
        class="tooltip"
        :style="{
          transform: `translate(${targetX - 20}px, ${targetY - 65}px)`,
        }"
      >
        {{ tooltipValue }}
      </div>
    </div>
  </div>
</template>

<!-- <script setup>
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
const circleGap = 70;

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
      const xCordinate = currentX + circleGap;
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

const canvasWidth = computed(() => props.values.length * circleGap + startX);

const drawCanvas = () => {
  const canvas = canvasRef.value;

  const width = canvasWidth.value;
  const scale = 5;

  canvas.width = width * scale;
  canvas.height = canvasHeight * scale;

  canvas.style.width = `${canvasWidth}px`;
  canvas.style.height = `${canvasHeight}px`;

  const ctx = canvas.getContext("2d");
  const pointsArr = valuesPoints.value;
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
  ctx.fillText(startChartValue.value, startX - 18, canvasHeight - 15);
  ctx.stroke();

  ctx.beginPath();
  ctx.moveTo(pointsArr[0].x, pointsArr[0].y);

  for (let i = 0; i < pointsArr.length - 1; i++) {
    const current = pointsArr[i];
    const next = pointsArr[i + 1];

    const cp1x = current.x + (next.x - current.x) / 2;
    const cp1y = current.y;

    const cp2x = current.x + (next.x - current.x) / 2;
    const cp2y = next.y;

    const last = pointsArr[pointsArr.length - 1];
    if (last) {
      ctx.fillText(last.value, last.x - 5, last.y - 30);
    }

    ctx.fillStyle = "#374151";
    ctx.lineWidth = 2;
    ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, next.x, next.y - 20);
    ctx.fillText(current.value, current.x - 5, i !== 0 ? current.y - 25 : current.y - 8);
    ctx.stroke();
  }

  pointsArr.forEach((point, idx) => {
    ctx.beginPath();
    ctx.arc(point.x, idx === 0 ? point.y : point.y - 18, 5, 0, Math.PI * 2);
    ctx.fillStyle = "#149ddd";
    ctx.fill();

    ctx.beginPath();
    ctx.font = "12";
    ctx.fillStyle = "#000";
    ctx.fillText(idx + 1, startX + idx * circleGap - 2, canvasHeight - 1);
    ctx.stroke();
  });
};

// Hover line
const targetX = ref(0);
const targetY = ref(0);
const isHovering = ref(false);
const tooltipValue = ref(0);

const onMove = (e) => {
  isHovering.value = true;
  const moveX = e.offsetX;
  const points = valuesPoints.value;

  for (let idx = 0; idx < points.length; idx++) {
    const parcent = (points[idx]?.x + points[idx + 1]?.x) / 2;

    if (idx === 0) {
      targetX.value = points[0].x;
      targetY.value = points[0].y;
      tooltipValue.value = points[0].value;
    }

    if (moveX > parcent) {
      targetX.value = points[idx + 1]?.x;
      targetY.value = points[idx + 1]?.y;
      tooltipValue.value = points[idx + 1].value;
    }
  }
};

const onLeave = () => {
  isHovering.value = false;
};

onMounted(() => {
  drawCanvas();
});
</script> -->

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
      circleGap: 70,

      // Hover line datas
      targetX: 0,
      targetY: 0,
      isHovering: false,
      tooltipValue: 0,
    };
  },

  computed: {
    canvasWidth() {
      return this.values.length * this.circleGap + this.startX;
    },

    onlyNumbers() {
      const isNumeric = (item) => /^[+-]?\d+(\.\d+)?$/.test(item);

      return this.values.filter((item) => isNumeric(String(item)) && item > 0).map((item) => Number(item));
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
          const xCordinate = currentX + this.circleGap;
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
    drawCanvas() {
      const canvas = this.$refs.canvasRef;

      const width = this.canvasWidth;
      const height = this.canvasHeight;
      const pixelRatio = 5;

      canvas.width = width * pixelRatio;
      canvas.height = height * pixelRatio;

      canvas.style.width = `${width}px`;
      canvas.style.height = `${height}px`;

      const ctx = canvas.getContext("2d");

      // End line
      ctx.beginPath();
      ctx.moveTo(this.startX * pixelRatio, 10 * pixelRatio);
      ctx.lineTo(width * pixelRatio, 10 * pixelRatio);
      ctx.strokeStyle = "#ccc";
      ctx.lineWidth = pixelRatio;
      ctx.font = `${10 * pixelRatio}px sans-serif`;
      ctx.fillStyle = "#374151";

      ctx.fillText(this.endChartValue, (this.startX - 18) * pixelRatio, 10 * pixelRatio);
      ctx.stroke();

      // Start line
      ctx.beginPath();
      ctx.moveTo(this.startX * pixelRatio, (height - 15) * pixelRatio);
      ctx.lineTo(width * pixelRatio, (height - 15) * pixelRatio);
      ctx.strokeStyle = "#ccc";
      ctx.lineWidth = pixelRatio;
      ctx.font = `${10 * pixelRatio}px sans-serif`;

      ctx.fillText(this.startChartValue, (this.startX - 18) * pixelRatio, (height - 15) * pixelRatio);
      ctx.stroke();

      const pointsArr = this.valuesPoints;
      if (pointsArr.length > 0) {
        ctx.beginPath();
        ctx.moveTo(pointsArr[0].x * pixelRatio, pointsArr[0].y * pixelRatio);

        for (let i = 0; i < pointsArr.length - 1; i++) {
          const current = pointsArr[i];
          const next = pointsArr[i + 1];

          const cp1x = current.x + (next.x - current.x) / 2;
          const cp1y = current.y;

          const cp2x = current.x + (next.x - current.x) / 2;
          const cp2y = next.y;

          ctx.bezierCurveTo(cp1x * pixelRatio, cp1y * pixelRatio, cp2x * pixelRatio, cp2y * pixelRatio, next.x * pixelRatio, (next.y - 20) * pixelRatio);

          ctx.fillStyle = "#374151";
          ctx.font = `${12 * pixelRatio}px sans-serif`;

          ctx.fillText(current.value, (current.x - 5) * pixelRatio, (i !== 0 ? current.y - 25 : current.y - 8) * pixelRatio);
        }

        ctx.strokeStyle = "#149ddd";
        ctx.lineWidth = 2 * pixelRatio;
        ctx.stroke();

        // Last value label
        const last = pointsArr[pointsArr.length - 1];

        ctx.fillStyle = "#374151";
        ctx.font = `${12 * pixelRatio}px sans-serif`;
        ctx.fillText(last.value, (last.x - 5) * pixelRatio, (last.y - 30) * pixelRatio);
      }

      pointsArr.forEach((point, idx) => {
        ctx.beginPath();
        ctx.arc(point.x * pixelRatio, (idx === 0 ? point.y : point.y - 18) * pixelRatio, 5 * pixelRatio, 0, Math.PI * 2);

        ctx.fillStyle = "#149ddd";
        ctx.fill();

        ctx.beginPath();
        ctx.font = `${12 * pixelRatio}px sans-serif`;
        ctx.fillStyle = "#000";

        ctx.fillText(idx + 1, (this.startX + idx * this.circleGap - 2) * pixelRatio, (height - 1) * pixelRatio);
      });
    },

    onMove(e) {
      this.isHovering = true;
      const moveX = e.offsetX;
      const points = this.valuesPoints;

      for (let idx = 0; idx < points.length; idx++) {
        const parcent = (points[idx]?.x + points[idx + 1]?.x) / 2;

        if (idx === 0) {
          this.targetX = points[0].x;
          this.targetY = points[0].y;
          this.tooltipValue = points[0].value;
        }

        if (moveX > parcent) {
          this.targetX = points[idx + 1]?.x;
          this.targetY = points[idx + 1]?.y;
          this.tooltipValue = points[idx + 1].value;
        }
      }
    },

    onLeave() {
      this.isHovering = false;
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
  position: relative;
  cursor: pointer;
}

.hover-line {
  position: absolute;
  top: 10px;
  left: 0;
  border-left: 2px dashed #94a3b8;
  transition: transform 0.3s ease-out;
}
.tooltip {
  position: absolute;
  left: 0;
  top: 0;
  padding: 5px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #e5e7eb;
  font-size: 12px;
  font-weight: 400;
  width: 30px;
  height: 30px;
  pointer-events: none;
  transition: transform 0.3s ease-out;
  background: #000;
}
</style>
