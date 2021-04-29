<template>
  <view class="container">
    <view class="wrap">
      <view class="item">
        <view class="title">
          <text>名称</text>
        </view>
        <view class="input">
          <u-input v-model="goodsInfo.name" />
        </view>
      </view>
      <view class="item">
        <view class="title">
          <text>价格</text>
        </view>
        <view class="input">
          <u-input v-model="goodsInfo.price" type="digit" />
        </view>
      </view>
    </view>
    <BottomBar>
      <view class="bottom-bar">
        <view class="btn confirm-btn" @click="confirm">
          <text>确认</text>
        </view>
      </view>
    </BottomBar>
  </view>
</template>

<script>
const db = wx.cloud.database()

export default {
  data() {
    return {
      goodsId: 0,
      goodsInfo: {},
    }
  },
  onLoad(options) {
    this.goodsId = options.id
    if (this.goodsId) {
      uni.setNavigationBarTitle({ title: '更新商品' })
      this.getGoodsInfo()
    }
  },
  methods: {
    getGoodsInfo() {
      db.collection('goods')
        .doc(this.goodsId)
        .get()
        .then(res => (this.goodsInfo = res.data))
        .catch(console.error)
    },
    confirm() {
      if (this.goodsId) {
        db.collection('goods')
          .doc(this.goodsId)
          .update({
            data: {
              name: this.goodsInfo.name,
              price: Number(this.goodsInfo.price),
              updateTime: db.serverDate()
            },
          })
          .then(() => {
            uni.showModal({
              content: '更新成功',
              showCancel: false,
              success: res => {
                if (res.confirm) {
                  this.$u.route({ type: 'back' })
                  uni.$emit('goodsCreate')
                }
              },
            })
          })
          .catch(console.error)
      } else {
        db.collection('goods')
          .add({
            data: {
              name: this.goodsInfo.name,
              price: Number(this.goodsInfo.price),
              createTime: db.serverDate()
            },
          })
          .then(() => {
            uni.showModal({
              content: '添加成功',
              showCancel: false,
              success: res => {
                if (res.confirm) {
                  this.$u.route({ type: 'back' })
                  uni.$emit('goodsCreate')
                }
              },
            })
          })
          .catch(console.error)
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.container {
  padding: 0 30rpx;

  .wrap {
    .item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      height: 100rpx;
      padding: 0 10rpx;
      border-bottom: 1rpx solid #eee;

      .title {
        margin-right: 30rpx;
      }

      .input {
        flex: 1;
      }
    }
  }

  .bottom-bar {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
    padding: 0 30rpx;

    .confirm-btn {
      width: 100%;
      height: 70rpx;
      font-size: 30rpx;
      color: #fff;
      background: $u-type-primary;
      border-radius: 35rpx;
    }
  }
}
</style>
