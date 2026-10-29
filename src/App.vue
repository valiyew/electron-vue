<script>
import "./index.css";
import VertualScrollBar from "./components/vertual-scroll-custom-scroll-bar/VertualScrollBar.vue";

export default {
  components: {
    VertualScrollBar,
  },

  // data() {
  //   return {
  //     values: [70, 30, 50, 40, 60, 35, 80, {}, 35, 60, 35, 40, "sdwdw", "100", 60, 35, 34, 34, 54, 65],
  //   };
  // },
};
</script>

<template>
  <h1>Vertual Scroll and Custom Scroll bar</h1>
  <div class="box">
    <VertualScrollBar />
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
