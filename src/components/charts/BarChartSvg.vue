<template>
  <div class="chart">
    <h1>Bar chart svg</h1>

    <div class="chart-scroll">
      <svg :width="svgWidth" :height="320" :viewBox="`0 0 ${svgWidth} ${320}`">
        <line :x1="startX" y1="20" :x2="svgWidth" y2="20" text-anchor="end" stroke="#ccc" />
        <text :x="startX - 10" y="20" font-size="10px" text-anchor="end">{{ endChartValue }}</text>

        <line :x1="startX" :y1="svgHeight" :x2="svgWidth" :y2="svgHeight" text-anchor="end" stroke="#ccc" />
        <text :x="startX - 10" :y="svgHeight" font-size="10px" text-anchor="end">{{ startChartValue }}</text>

        <g v-for="(point, idx) in valuesPoints" :key="idx">
          <rect :width="barWidth" :height="getBarHeight(point.value)" :x="point.x" :y="svgHeight - getBarHeight(point.value)" ry="4" rx="4" fill="blue" />

          <text :x="point.x + 18" :y="point.y - 20" text-anchor="middle" font-size="10px">{{ point.value }}</text>
        </g>
        <text v-for="(item, idx) in values" :key="idx" :x="startX + 17 + idx * (barWidth + 20)" :y="svgHeight + 15" text-anchor="middle" font-size="10">
          {{ idx + 1 }}
        </text>
      </svg>
    </div>
  </div>
</template>

<!-- <script setup>
import { ref, computed } from "vue";

const props = defineProps({
  values: {
    type: Array,
    required: true,
  },
});

const svgHeight = 300;
const startX = 40;
const barWidth = 35;

const svgWidth = computed(() => props.values.length * (barWidth + 20));

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

function getBarHeight(value) {
  const min = startChartValue.value;
  const max = endChartValue.value;

  const result = (value - min) / (max - min); // 43 / 61 = 0.7 * 300 = 210

  return result * svgHeight;
}

const valuesPoints = computed(() => {
  let currentX = startX;
  let currentY = 0;
  const points = [];

  onlyNumbers.value.map((item, idx) => {
    if (idx !== 0) {
      const xCordinate = currentX + barWidth + 20;
      currentX = xCordinate;
    }
    const yCordinate = svgHeight - ((item - startChartValue.value) / (endChartValue.value - startChartValue.value)) * (svgHeight - 20);
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
      svgHeight: 300,
      startX: 40,
      barWidth: 35,
    };
  },

  computed: {
    svgWidth() {
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
        const yCordinate = this.svgHeight - ((item - this.startChartValue) / (this.endChartValue - this.startChartValue)) * (this.svgHeight - 20);
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

      return result * this.svgHeight;
    },
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
}
</style>
