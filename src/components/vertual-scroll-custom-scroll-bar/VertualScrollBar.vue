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
        <div :style="{ height: topHeight + 'px' }"></div>

        <div class="user-card" v-for="(item, index) in visibleItems" :key="item.id" ref="itemsRef">
          <div class="about-user">
            <strong class="user-name">
              <img :src="item.image" :alt="item.name" class="user-image" />

              {{ item.name }}

              <svg width="15" height="15" viewBox="0 0 40 40" xmlns="http://www.w3.org/2000/svg">
                <path
                  fill="#0095F6"
                  d="M20 0.5L24.2 2.5L28.8 1.8L31.5 5.5L35.8 7.2L36.5 11.8L39.5 15.5L38 20L39.5 24.5L36.5 28.2L35.8 32.8L31.5 34.5L28.8 38.2L24.2 37.5L20 39.5L15.8 37.5L11.2 38.2L8.5 34.5L4.2 32.8L3.5 28.2L0.5 24.5L2 20L0.5 15.5L3.5 11.8L4.2 7.2L8.5 5.5L11.2 1.8L15.8 2.5Z"
                />
                <path d="M11 20.5L17.2 26.5L29.5 13.5" fill="none" stroke="white" strokeWidth="4" strokeLinecap="round" strokeLinejoin="round" />
              </svg>
            </strong>

            <p class="user-job">Job: {{ item.job }}</p>
            <p class="user-age">Age: {{ item.age }}</p>
          </div>

          <p class="user-desc">{{ item.description }}</p>

          <div class="post-about">
            <svg @click="item.isLiked = !item.isLiked" class="heartIcon" width="18px" height="18px" viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg">
              <path
                d="M16 28.5c-.4 0-.8-.1-1.1-.3C7.4 23.3 2 17.5 2 11.2 2 6.6 5.6 3 10.2 3c2.7 0 5.2 1.3 6.8 3.5C18.6 4.3 21.1 3 23.8 3 28.4 3 32 6.6 32 11.2c0 6.3-5.4 12.1-12.9 17-.3.2-.7.3-1.1.3z"
                :fill="item.isLiked ? 'red' : 'none'"
                stroke="#ccc"
              />
            </svg>

            <svg @click="handleClickComment(item.comments)" class="commentIcon" width="20px" height="20px" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path
                d="M20.5 11.5C20.5 16.2 16.7 20 12 20C10.5 20 9.1 19.6 7.9 19L3 20L4.5 15.5C3.9 14.3 3.5 12.9 3.5 11.5C3.5 6.8 7.3 3 12 3C16.7 3 20.5 6.8 20.5 11.5Z"
                stroke="#ccc"
              />
            </svg>

            <svg @click="item.isShared = !item.isShared" class="shareIcon" width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M17 2L21 6L17 10" :stroke="item.isShared ? 'blue' : '#ccc'" strokeWidth="1.8" strokeLinecap="round" strokeLinejoin="round" />
              <path d="M3 11V9C3 7.34 4.34 6 6 6H21" :stroke="item.isShared ? 'blue' : '#ccc'" strokeWidth="1.8" strokeLinecap="round" strokeLinejoin="round" />
              <path d="M7 22L3 18L7 14" :stroke="item.isShared ? 'blue' : '#ccc'" strokeWidth="1.8" strokeLinecap="round" strokeLinejoin="round" />
              <path d="M21 13V15C21 16.66 19.66 18 18 18H3" :stroke="item.isShared ? 'blue' : '#ccc'" strokeWidth="1.8" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </div>
        </div>

        <div :style="{ height: bottomHeight + 'px' }"></div>

        <!-- No data -->
        <div v-if="values.length === 0" class="no-data">
          <p>No data available</p>
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
        <div :style="{ height: thumbHeight > 10 ? `${thumbHeight}px` : `10px`, top: `${thumbTop}px` }" class="thumb" @mousedown.stop="handleDrag"></div>
      </div>
    </div>
  </div>

  <Transition name="fade-v">
    <div v-if="showCommentModal" class="commentsModal">
      <div class="modal-header">
        <h3>Comments</h3>
        <button @click="showCommentModal = false">X</button>
      </div>

      <div v-if="isLoading" v-for="n in 4" :key="n" class="commentsCard skeleton">
        <div class="headerCommenter">
          <div class="skeletonAvatar"></div>
          <div class="skeletonName"></div>
          <div class="skeletonBadge"></div>
        </div>

        <div class="skeletonText"></div>
      </div>
      <div v-else v-for="comment in userComments" :key="comment.id" class="commentsCard">
        <div class="headerCommenter">
          <img :src="comment.image" :alt="comment.name" />
          <strong>{{ comment.name }}</strong>

          <svg width="15" height="15" viewBox="0 0 40 40" xmlns="http://www.w3.org/2000/svg">
            <path
              fill="#0095F6"
              d="M20 0.5L24.2 2.5L28.8 1.8L31.5 5.5L35.8 7.2L36.5 11.8L39.5 15.5L38 20L39.5 24.5L36.5 28.2L35.8 32.8L31.5 34.5L28.8 38.2L24.2 37.5L20 39.5L15.8 37.5L11.2 38.2L8.5 34.5L4.2 32.8L3.5 28.2L0.5 24.5L2 20L0.5 15.5L3.5 11.8L4.2 7.2L8.5 5.5L11.2 1.8L15.8 2.5Z"
            />
            <path d="M11 20.5L17.2 26.5L29.5 13.5" fill="none" stroke="white" strokeWidth="4" strokeLinecap="round" strokeLinejoin="round" />
          </svg>
        </div>

        <p>{{ comment.text }}</p>
      </div>
    </div>
  </Transition>
  <div v-if="showCommentModal" class="backdropFilter"></div>
</template>

<script>
export default {
  data() {
    return {
      values: Array.from({ length: 100 }, (_, index) => ({
        id: index + 1,
        name: ["Davron", "Ali", "Vali", "Said", "Jamshid", "Johon", "Tony Stark", "John Doe", "Jane Smith", "Robert Johnson"][Math.floor(Math.random() * 10)],
        age: 20 + (index % 11),
        job: ["Frontend Developer", "Backend Developer", "UI/UX Designer", "Project Manager", "Fullstack Developer"][Math.floor(Math.random() * 5)],
        image: `https://i.pravatar.cc/150?img=${(index % 70) + 1}`,
        isLiked: [false, true][Math.floor(Math.random() * 2)],
        isShared: [false, true][Math.floor(Math.random() * 2)],
        description:
          index % 3 === 0
            ? `Lorem ipsum dolor sit amet consectetur adipisicing elit. Voluptas, ducimus?`
            : `Lorem ipsum dolor sit amet consectetur adipisicing elit. Voluptas, ducimus ipsum dolor sit amet consectetur adipisicing elit. Voluptas ipsum dolor sit amet consectetur adipisicing elit. Voluptasipsum dolor sit amet consectetur adipisicing elit. Voluptas?`,

        comments: Array.from({ length: Math.floor(Math.random() * 5) + 1 }, (_, i) => ({
          id: i + 1,
          name: ["Alex", "John", "David", "Michael", "Oliver"][Math.floor(Math.random() * 5)],
          text: [
            "Falonchini sapali storismda kirb korila 😍",
            "Storismga like bosvorilaaa 🔥",
            "( Pizdes qotb kuletgan stiker )",
            "🔥😍🔥",
            "Bekzod aka, blya...",
            "Baxtli bo‘l, atlet!",
            "Qo'yganman! Kimga ? Hokiminga...",
            "Maaazgiii😂",
          ][Math.floor(Math.random() * 8)],
          image: `https://i.pravatar.cc/150?img=${Math.floor(Math.random() * 70) + 1}`,
        })),
      })),
      contentWidth: 600,
      contentHeight: 800,
      thumbHeight: 0,
      thumbTop: 0,
      isHovering: false,
      scrollTimer: null,
      isScrolling: false,
      isLiked: false,
      isShared: false,
      showCommentModal: false,
      userComments: [],
      isLoading: false,

      // Vertikal skrol
      itemHeight: 0,
      startIndex: 0,
    };
  },

  computed: {
    visibleCount() {
      return Math.ceil(this.contentHeight / this.itemHeight);
    },

    visibleItems() {
      const start = this.startIndex;
      const visibleCount = this.visibleCount;

      return this.values.slice(start, start + visibleCount * 2);
    },

    topHeight() {
      return this.startIndex * this.itemHeight;
    },

    bottomHeight() {
      return (this.values.length - this.startIndex - this.visibleItems.length) * this.itemHeight;
    },
  },

  mounted() {
    const contentRef = this.$refs.contentRef;
    const trackRef = this.$refs.trackRef;

    const scrollHeight = contentRef.scrollHeight;
    const clientHeight = contentRef.clientHeight;
    const trackClientHeight = trackRef.clientHeight;

    // Item heightini hisoblash
    this.itemHeight = contentRef.scrollHeight / this.values.length;

    // Birinchi renderda thumbni heightini korsatish un
    this.thumbHeight = (clientHeight / scrollHeight) * trackClientHeight;
  },

  methods: {
    handleScroll() {
      this.isScrolling = true;
      const contentRef = this.$refs.contentRef;
      const trackRef = this.$refs.trackRef;

      const scrollHeight = contentRef.scrollHeight;
      const clientHeight = contentRef.clientHeight;
      const scrollTop = contentRef.scrollTop;
      const trackClientHeight = trackRef.clientHeight;

      const result = (scrollTop / (clientHeight - scrollHeight)) * (trackClientHeight - this.thumbHeight);
      this.thumbTop = Math.abs(result);

      clearTimeout(this.scrollTimer);

      this.scrollTimer = setTimeout(() => {
        this.isScrolling = false;
      }, 1200);

      // Vertual skrol
      this.startIndex = Math.floor(scrollTop / this.itemHeight);

      // Thumb height calculation
      this.thumbHeight = (clientHeight / scrollHeight) * trackClientHeight;
    },

    handleClick(e) {
      console.log(e);
      const contentRef = this.$refs.contentRef;
      const trackRef = this.$refs.trackRef;
      const cordinateY = e.clientY - trackRef.getBoundingClientRect().top;

      contentRef.scrollTop = (cordinateY / this.contentHeight) * contentRef.scrollHeight - 250;
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

    handleClickComment(itemComments) {
      this.isLoading = true;
      this.showCommentModal = true;
      this.userComments = itemComments;

      setTimeout(() => {
        this.isLoading = false;
      }, 1000);
    },
  },
};
// 840 - 13.079 = -12.239;
// 50 / -12.239 = 0.004;
// 800 - 51 = 749;
// 0.004 * 749 = 2.9
</script>

<style scoped>
.fade-v-enter-active,
.fade-v-leave-active {
  transition: opacity 0.5s ease;
}

.fade-v-enter-from,
.fade-v-leave-to {
  opacity: 0;
}

.commentsModal {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 600px;
  height: 400px;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
  z-index: 999;
  overflow-y: auto;
}
.backdropFilter {
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  position: absolute;
  top: 0;
  left: 0;
  z-index: 998;
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.modal-header button {
  background: none;
  font-size: 18px;
  font-weight: 500;
  border: none;
  cursor: pointer;
}

.commentsCard {
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 10px;
  margin-bottom: 10px;
}

.headerCommenter {
  display: flex;
  align-items: center;
  gap: 10px;
}
.headerCommenter img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

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
.user-name {
  font-size: 18px;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 10px;
}

.user-image {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

.post-about {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 15px;
  justify-content: end;
  margin-bottom: 8px;
}

.no-data {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.heartIcon,
.commentIcon,
.shareIcon {
  cursor: pointer;
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

.skeletonAvatar,
.skeletonName,
.skeletonBadge,
.skeletonText {
  background: #e0e0e0;
  animation: skeleton-loading 1.2s infinite ease-in-out;
  border-radius: 6px;
}

.skeletonAvatar {
  width: 35px;
  height: 35px;
  border-radius: 50%;
}

.skeletonName {
  width: 100px;
  height: 14px;
}

.skeletonBadge {
  width: 15px;
  height: 15px;
  border-radius: 50%;
}

.skeletonText {
  width: 80%;
  height: 12px;
  margin-top: 12px;
}

@keyframes skeleton-loading {
  0%,
  100% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
}
</style>
