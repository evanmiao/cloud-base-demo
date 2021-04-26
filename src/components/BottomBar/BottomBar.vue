<template>
  <view class="bottom-bar">
    <view class="placeholder safe-area-inset-bottom"></view>
    <view class="bar safe-area-inset-bottom"><slot></slot></view>
  </view>
</template>

<script>
export default {
  name: 'BottomBar',
}
</script>

<style lang="scss" scoped>
.bottom-bar {
  .placeholder {
    height: 120rpx;
    box-sizing: content-box;
  }

  .bar {
    @extend .placeholder;
    position: fixed;
    z-index: 1000;
    left: 0;
    bottom: 0;
    width: 100%;
    background: #fff;
    box-shadow: 0 0 10rpx rgba(37, 37, 37, 0.08);
  }
}
</style>
