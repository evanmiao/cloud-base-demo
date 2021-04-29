<template>
  <view class="container">
    <view class="filter">
      <u-dropdown
        title-size="26"
        menu-icon="arrow-down-fill"
        menu-icon-size="14"
      >
        <u-dropdown-item
          v-model="orderCurrent"
          :title="orderOptions.find(v => v.value === orderCurrent).label"
          :options="orderOptions"
          @change="refreshGoodsList"
        ></u-dropdown-item>
        <u-dropdown-item
          v-model="priceCurrent"
          :title="priceOptions.find(v => v.value === priceCurrent).label"
          :options="priceOptions"
          @change="refreshGoodsList"
        ></u-dropdown-item>
      </u-dropdown>
    </view>
    <view v-if="!isEmpty" class="wrap">
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
      <view class="loadmore">
        <u-loadmore :status="status" />
      </view>
    </view>
    <view v-else class="empty">
      <u-empty></u-empty>
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
const _ = db.command

export default {
  data() {
    return {
      goodsList: [],
      page: 1,
      status: 'loadmore',
      isEmpty: false,
      orderCurrent: 0,
      priceCurrent: 0,
      orderOptions: [
        { label: '默认排序', value: 0 },
        { label: '最新发布', value: 1 },
        { label: '价格低到高', value: 2 },
        { label: '价格高到低', value: 3 },
      ],
      priceOptions: [
        { label: '价格不限', value: 0 },
        { label: '10元以内', value: 1 },
        { label: '10-100元', value: 2 },
        { label: '100元以上', value: 3 },
      ],
    }
  },
  onLoad(options) {
    this.getGoodsList()
    uni.$on('goodsCreate', this.refreshGoodsList)
  },
  onUnload() {
    uni.$off('goodsCreate')
  },
  onPullDownRefresh() {
    this.refreshGoodsList()
  },
  onReachBottom() {
    if (this.status === 'nomore') {
      this.$u.toast('没有更多了')
      return
    }
    this.getGoodsList()
  },
  methods: {
    refreshGoodsList() {
      this.page = 1
      this.isEmpty = false
      this.goodsList = []
      this.getGoodsList()
    },
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
                this.refreshGoodsList()
                this.$u.toast('删除成功')
              })
              .catch(console.error)
          }
        },
      })
    },
    getGoodsList() {
      const LIMIT = 10
      let field, order
      switch (this.orderCurrent) {
        case 1:
          field = 'createTime'
          order = 'desc'
          break
        case 2:
          field = 'price'
          order = 'desc'
          break
        case 3:
          field = 'price'
          order = 'asc'
          break
        default:
          field = 'createTime'
          order = 'asc'
          break
      }
      let where = {}
      switch (this.priceCurrent) {
        case 1:
          where.price = _.lt(10)
          break
        case 2:
          where.price = _.gte(10).and(_.lt(100))
          break
        case 3:
          where.price = _.gte(100)
          break
        default:
          delete where.price
          break
      }
      db.collection('goods')
        .where(where)
        .orderBy(field, order)
        .limit(LIMIT)
        .skip((this.page++ - 1) * LIMIT)
        .get()
        .then(res => {
          uni.stopPullDownRefresh()
          if (res.data.length === 0) {
            if (this.page === 2) this.isEmpty = true
            this.status = 'nomore'
            return
          }
          this.status = 'loadmore'
          this.goodsList.push(...res.data)
        })
        .catch(console.error)
    },
  },
}
</script>

<style lang="scss" scoped>
.container {
  .wrap {
    padding: 0 30rpx;

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

    .loadmore {
      padding: 30rpx 0;
    }
  }

  .empty {
    height: 100vh;
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
