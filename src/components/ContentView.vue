<template>
  <transition name="slide-right">
    <div class="content">
      <div class="content-wrapper" v-if="bookAvailable">
        <div
          class="content-item"
          v-for="(item, index) in navigation.toc"
          :key="index"
          @click="jumpTo(item.href)"
        >
          <span class="text">{{item.label}}</span>
        </div>
      </div>
      <div class="empty" v-else>加载中...</div>
    </div>
  </transition>
</template>
<script>
export default {
  props: {
    bookAvailable: {
      type: Boolean,
      default: false
    },
    navigation: Object,
    isShowContent: Boolean
  },
  methods: {
    jumpTo(target) {
      this.$emit("jumpTo", target);
    }
  }
};
</script>
<style lang="scss" scoped>
@import "@/assets/styles/global";
.content {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 102;
  width: 80%;
  height: 100%;
  background: #fff;
  overflow: scroll;
  .content-wrapper {
    .content-item {
      border-bottom: px2rem(1) solid #ccc;
      padding-bottom: px2rem(15);
      padding-left: px2rem(20);
      .text {
        font-size: px2rem(12);
      }
    }
  }
  .empty {
    height: 100%;
    font-size: px2rem(14);
    @include center;
  }
}
</style>