<template>
  <div class="chart">
    <h1>Line chart svg</h1>

    <div class="chart-scroll">
      <svg :width="svgWidth" :height="svgHeight" :viewBox="`0 0 ${svgWidth} ${svgHeight}`" xmlns="http://www.w3.org/2000/svg" @mousemove="onMove" @mouseleave="onLeave">
        <!-- End value line -->
        <line :x1="startX" y1="20" :x2="svgWidth" y2="20" stroke="#ccc" />
        <text :x="startX - 10" y="20" font-size="10px" text-anchor="end">{{ endChartValue }}</text>

        <!-- Start value line -->
        <line :x1="startX" :y1="svgHeight - 20" :x2="svgWidth" :y2="svgHeight - 20" stroke="#ccc" />
        <text :x="startX - 10" :y="svgHeight - 20" font-size="10px" text-anchor="end">{{ startChartValue }}</text>

        <!-- Hover line -->
        <line
          class="hover-line"
          v-if="isHovering"
          x1="0"
          :y1="20"
          x2="0"
          :y2="svgHeight - 20"
          stroke="#ccc"
          stroke-dasharray="4 4"
          stroke-width="1"
          :style="{ transform: `translateX(${targetX}px)` }"
        />

        <g v-for="(point, idx) in valuesPoints" :key="idx">
          <path :d="linePath()" stroke="#2563eb" fill="none" stroke-width="2" />
          <circle v-if="point.value" class="circle" :cx="point.x" :cy="point.y" r="5" />

          <text :x="point.x" :y="point.y - 10" font-size="10px" text-anchor="middle">{{ point.value }}</text>
        </g>

        <text v-for="(item, idx) in values" :key="idx" :x="getXCordinate(idx)" :y="svgHeight" font-size="10px" text-anchor="middle">{{ idx + 1 }}</text>
      </svg>

      <!-- Tooltip -->
      <div
        v-if="isHovering"
        class="tooltip"
        :style="{
          transform: `translate(${targetX - 20}px, ${targetY - 50}px)`,
        }"
      >
        {{ tooltipValue }}
      </div>
    </div>
  </div>
</template>

<!-- <script setup>
import { computed, ref } from "vue";

const props = defineProps({
  values: {
    type: Array,
    required: true,
  },
});
const svgHeight = 300;

const startX = 40;
const circleGap = 70;
const targetX = ref(0);
const targetY = ref(0);
const isHovering = ref(false);
const tooltipValue = ref(0);

const svgWidth = computed(() => props.values.length * circleGap - 40);

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

const getXCordinate = (idx) => {
  return startX + idx * circleGap;
};

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
    const yCordinate = svgHeight - 25 - ((item - startChartValue.value) / (endChartValue.value - startChartValue.value)) * (svgHeight - 50);
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

const linePath = () => {
  const pointsArr = valuesPoints.value;

  let result = `M ${pointsArr[0].x} ${pointsArr[0].y}`;

  for (let idx = 0; idx < pointsArr.length - 1; idx++) {
    const current = pointsArr[idx];
    const next = pointsArr[idx + 1];

    const cp1x = current.x + (next.x - current.x) / 2;
    const cp1y = current.y;

    const cp2x = current.x + (next.x - current.x) / 2;
    const cp2y = next.y;

    result = result + ` C ${cp1x} ${cp1y}, ${cp2x} ${cp2y}, ${next.x} ${next.y}`;
  }

  return result;
};

// M 0 0 L 10 10 L 20 0
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
      circleGap: 70,
      targetX: 0,
      targetY: 0,
      isHovering: false,
      tooltipValue: 0,
      isDrawing: false,
    };
  },

  computed: {
    svgWidth() {
      return this.values.length * this.circleGap;
    },

    onlyNumbers() {
      const isNumeric = (item) => /^[+-]?\d+(\.\d+)?$/.test(item);

      return this.values.map((item) => (isNumeric(String(item)) && item > 0 ? Number(item) : null));
    },

    startChartValue() {
      if (this.onlyNumbers.length === 0) return;

      const minValue = Math.min(...this.onlyNumbers);

      return Math.round(minValue - minValue * 0.1);
    },

    endChartValue() {
      if (this.onlyNumbers.length === 0) return;

      const maxValue = Math.max(...this.onlyNumbers);

      return Math.round(maxValue * 0.1 + maxValue);
    },

    valuesPoints() {
      let currentX = this.startX;
      let currentY = 0;
      const points = [];

      console.log(this.onlyNumbers);

      this.onlyNumbers.map((item, idx) => {
        if (idx !== 0) {
          const xCordinate = currentX + this.circleGap;
          currentX = xCordinate;
        }

        const yCordinate = this.svgHeight - 25 - ((item - this.startChartValue) / (this.endChartValue - this.startChartValue)) * (this.svgHeight - 50);
        currentY = yCordinate;

        const pointsBox = {
          value: item,
          x: currentX,
          y: item === null ? null : currentY,
        };

        points.push({ ...pointsBox });
      });

      return points;
    },
  },

  methods: {
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

    getXCordinate(idx) {
      return this.startX + idx * this.circleGap;
    },

    linePath() {
      const pointsArr = this.valuesPoints;

      let result = "";

      for (let idx = 0; idx < pointsArr.length - 1; idx++) {
        const current = pointsArr[idx];
        const next = pointsArr[idx + 1];

        if (current.y !== null && next.y !== null) {
          const cpx = current.x + (next.x - current.x) / 2;
          const cp1y = current.y;
          const cp2y = next.y;

          result = result + `M ${current.x} ${current.y} C ${cpx} ${cp1y}, ${cpx} ${cp2y}, ${next.x} ${next.y}`;
        }
      }

      return result;
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
  cursor: pointer;
  position: relative;
}
.circle {
  fill: #2563eb;
  transition: r 0.15s ease;
}
.hover-line {
  transition: transform 0.3s ease-out;
  fill: #94a3b8;
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
