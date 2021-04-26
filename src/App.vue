<script>
export default {
  onLaunch() {
    console.log("App Launch")
    if (!wx.cloud) {
      console.error('请使用 2.2.3 或以上的基础库以使用云能力')
    } else {
      wx.cloud.init({ traceUser: true })
    }
  },
  onShow() {
    console.log("App Show")
  },
  onHide() {
    console.log("App Hide")
  }
}
</script>

<style lang="scss">
@import "uview-ui/index.scss";

image {
  display: block;
  width: 100%;
  height: 100%;
}

.btn {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
