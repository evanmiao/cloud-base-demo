<template>
  <view class="container">
    <view class="wrap">
      <view v-for="(item, index) in goodsList" :key="item._id" class="item">
        <view class="info">
          <text>{{ item.name }} / ${{ item.price }}</text>
        </view>
        <view class="btn-wrap">
          <view class="btn update-btn" @click.stop="updateGoods(item._id)">
            <text>更新</text>
          </view>
          <view class="btn delete-btn" @click.stop="deleteGoods(item._id, index)">
            <text>删除</text>
          </view>
        </view>
      </view>
    </view>
    <BottomBar>
      <view class="bottom-bar">
        <view class="btn create-btn" @click="createGoods">
          <text>添加商品</text>
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
      goodsList: [],
    }
  },
  onLoad(options) {
    this.getGoodsList()
    uni.$on('goodsCreate', this.getGoodsList)
  },
  onUnload() {
    uni.$off('goodsCreate')
  },
  methods: {
    createGoods() {
      this.$u.route('pages/create/create')
    },
    updateGoods(id) {
      this.$u.route('pages/create/create', { id })
    },
    deleteGoods(id, index) {
      uni.showModal({
        title: '您确定要删除此商品吗？',
        content: '您不能撤销此操作。',
        success: r => {
          if (r.confirm) {
            db.collection('goods')
              .doc(id)
              .remove()
              .then(() => {
                this.goodsList.splice(index, 1)
                this.$u.toast('删除成功')
              })
              .catch(console.error)
          }
        },
      })
    },
    getGoodsList() {
      db.collection('goods')
        .get()
        .then(res => (this.goodsList = res.data))
        .catch(console.error)
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

  .bottom-bar {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
    padding: 0 30rpx;

    .create-btn {
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
