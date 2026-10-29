<template>
  <div class="container">
    <div class="userBox">
      <div
        ref="contentRef"
        class="content"
        @scroll="handleScroll"
        @mouseleave="
          {
            ((isHovering = false), (isScrolling = false));
          }
        "
        :style="{ width: `${contentWidth}px`, height: `${contentHeight}px` }"
      >
        <div class="user-card" v-for="(item, index) in values" :key="item.id">
          <div class="about-user">
            <strong class="user-name">{{ item.name }}</strong>
            <p class="user-job">Job: {{ item.job }}</p>
            <p class="user-age">Age: {{ item.age }}</p>
          </div>

          <p class="user-desc">{{ item.description }}</p>
        </div>
      </div>

      <!-- Custom scrollbar -->
      <div
        ref="trackRef"
        class="track"
        :style="{ height: `${contentHeight}px`, opacity: isHovering || isScrolling ? 1 : 0 }"
        @click="handleClick"
        @mouseenter="isHovering = true"
        @mouseleave="isHovering = false"
      >
        <div :style="{ height: `${thumbHeight}px`, top: `${thumbTop}px` }" class="thumb" @mousedown="handleDrag"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      values: Array.from({ length: 100 }, (_, index) => ({
        id: index + 1,
        name: `User ${index + 1}`,
        age: 20 + (index % 11),
        job: "Frontend developer",
        description: "  Lorem ipsum dolor sit amet consectetur adipisicing elit. Voluptas, ducimus?",
      })),
      contentWidth: 600,
      contentHeight: 800,
      thumbHeight: 0,
      thumbTop: 0,
      isHovering: false,
      scrollTimer: null,
      isScrolling: false,
    };
  },

  methods: {
    handleScroll() {
      const contentRef = this.$refs.contentRef;
      const trackRef = this.$refs.trackRef;

      const scrollHeight = contentRef.scrollHeight;
      const clientHeight = contentRef.clientHeight;
      const scrollTop = contentRef.scrollTop;
      const trackClientHeight = trackRef.clientHeight;

      const result = (scrollTop / (clientHeight - scrollHeight)) * (trackClientHeight - this.thumbHeight);
      this.thumbTop = Math.abs(result);

      this.isScrolling = true;

      clearTimeout(this.scrollTimer);

      this.scrollTimer = setTimeout(() => {
        this.isScrolling = false;
      }, 1200);
    },

    handleClick(e) {
      const contentRef = this.$refs.contentRef;
      contentRef.scrollTop = (e.offsetY / this.contentHeight) * contentRef.scrollHeight;
    },

    handleDrag(e) {
      const contentRef = this.$refs.contentRef;

      const startY = e.clientY;
      const startScroll = contentRef.scrollTop;
      const trackRef = this.$refs.trackRef;

      const move = (e) => {
        const deltaY = e.clientY - startY;
        const ratio = (contentRef.scrollHeight - contentRef.clientHeight) / (trackRef.clientHeight - this.thumbHeight);

        // Contentni ichini thumbni qimirlatganda skrol qilish uchun
        contentRef.scrollTop = startScroll + deltaY * ratio;
      };

      const stop = () => {
        document.removeEventListener("mousemove", move);
        document.removeEventListener("mouseup", stop);
      };

      document.addEventListener("mousemove", move);
      document.addEventListener("mouseup", stop);
    },
  },

  computed: {
    visibleItems() {},
  },

  mounted() {
    if (this.$refs.contentRef) {
      const contentRef = this.$refs.contentRef;
      const trackRef = this.$refs.trackRef;

      const scrollHeight = contentRef.scrollHeight;
      const clientHeight = contentRef.clientHeight;
      const trackClientHeight = trackRef.clientHeight;

      this.thumbHeight = (clientHeight / scrollHeight) * trackClientHeight;
    }
  },
};
// 840 - 13.079 = -12.239;
// 50 / -12.239 = 0.004;
// 800 - 51 = 749;
// 0.004 * 749 = 2.9
</script>

<style scoped>
.container {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  user-select: none;
}

.userBox {
  position: relative;
}

.content {
  border: 1px dotted #898888;
  padding: 20px 25px;
  border-radius: 20px;
  box-shadow: 1px 1px 5px 1px #898888;
  overflow-y: auto;
}

.content::-webkit-scrollbar {
  display: none;
}

.user-card {
  border-bottom: 1px solid #ccc;
  margin-bottom: 10px;
}

.about-user {
  display: flex;
  align-items: center;
  gap: 10px;
}

.track {
  position: absolute;
  top: 20px;
  right: 5px;
  width: 9px;
  background: #e5e7eb;
  border-radius: 10px;
  transition: 0.1s ease-out;
  cursor: pointer;
}

.thumb {
  position: absolute;
  left: 0;
  width: 100%;
  background: #6b7280;
  border-radius: 10px;
  cursor: pointer;
  transition: 0.1s ease-out;
}

.thumb:hover {
  background: #494d57;
}

.track:hover {
  width: 12px;
}
</style>
