<template>
  <view class="goods-item">
    <view class="info">
      <view class="avatar">
        <image :src="getItem.avatarUrl" mode="aspectFill"></image>
      </view>
      <view class="name">
        <text>{{ getItem.nickName }}</text>
      </view>
      <view class="date">
        <view class="icon">
          <u-icon name="clock"></u-icon>
        </view>
        <view class="text">
          <text>{{ $u.date(getItem.updateTime) }}</text>
        </view>
      </view>
    </view>
    <view class="title">
      <text>{{ getItem.name }}</text>
    </view>
    <view class="content">
      <text>{{ getItem.desc }}</text>
    </view>
    <view class="images" v-if="getItem.images">
      <view class="image" v-for="(image, index) in getItem.images" :key="index">
        <image :src="image" mode="aspectFill"></image>
      </view>
    </view>
    <view class="bottom">
      <view class="price">
        <text>${{ getItem.price }}</text>
      </view>
      <view class="btn-wrap">
        <view class="btn update-btn" @click.stop="$emit('update', item._id)">
          <text>更新</text>
        </view>
        <view class="btn delete-btn" @click.stop="$emit('delete', item._id)">
          <text>删除</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  name: 'GoodsItem',
  props: {
    item: {
      type: Object,
      default() {
        return {}
      },
    },
  },
  computed: {
    getItem() {
      return this.item
    },
  },
}
</script>

<style lang="scss" scoped>
.goods-item {
  padding: 30rpx;
  margin-top: 30rpx;
  box-shadow: 0 0 13rpx rgba(2, 85, 155, 0.07);
  border-radius: 10rpx;

  .info {
    display: flex;
    align-items: center;
    justify-content: space-between;

    .avatar {
      width: 70rpx;
      height: 70rpx;
      margin-right: 14rpx;
    }

    .name {
      font-size: 30rpx;
    }

    .date {
      display: flex;
      align-items: center;
      justify-content: center;
      margin-left: auto;
      color: #666;

      .text {
        margin-left: 10rpx;
        font-size: 26rpx;
      }
    }
  }

  .title {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    margin-top: 30rpx;
    font-size: 36rpx;
    font-weight: 450;
  }

  .content {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    margin-top: 24rpx;
    line-height: 1.6;
  }

  .images {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(auto-fill, 196rpx);
    gap: 20rpx;
    margin-top: 20rpx;
  }

  .bottom {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 30rpx;

    .price {
      font-size: 36rpx;
      font-weight: 500;
      color: $u-type-error;

      &::first-letter {
        font-size: 24rpx;
      }
    }

    .btn-wrap {
      display: flex;

      .update-btn {
        height: 50rpx;
        padding: 0 20rpx;
        background: $u-type-primary;
        color: #fff;
        border-radius: 10rpx;
      }

      .delete-btn {
        @extend .update-btn;
        margin-left: 20rpx;
        background: $u-type-error;
      }
    }
  }
}
</style>
