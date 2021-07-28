<template>
  <view class="image-upload">
    <view
      class="image"
      v-for="(image, index) in existingImageList.concat(imageList)"
      :key="index"
      @click="previewImage(index)"
    >
      <image :src="image" mode="aspectFill"></image>
      <view class="close" @click.stop="removeImage(index)">
        <u-icon name="close" size="28" color="#fff"></u-icon>
      </view>
    </view>
    <view
      v-if="existingImageList.length + imageList.length < maxCount"
      class="btn upload-btn"
      :style="{ background: bgColor }"
      @click="addImages"
    >
      <slot>
        <u-icon :name="uploadIcon" :size="iconSize" :color="iconColor"></u-icon>
      </slot>
    </view>
  </view>
</template>

<script>
export default {
  name: 'ImageUpload',
  props: {
    value: {
      type: Array,
      default() {
        return []
      },
    },
    preset: {
      type: Array,
      default() {
        return []
      },
    },
    maxCount: {
      type: Number,
      default: 9,
    },
    resultType: {
      type: String,
      default: 'file',
    },
    uploadIcon: {
      type: String,
      default: 'camera',
    },
    iconSize: {
      type: String,
      default: '80',
    },
    iconColor: {
      type: String,
      default: '#c1c1c1',
    },
    bgColor: {
      type: String,
      default: '#f8f8f8',
    },
  },
  computed: {
    imageList() {
      return this.value
    },
    existingImageList() {
      return this.preset
    },
  },
  methods: {
    addImages() {
      uni.chooseImage({
        count:
          this.maxCount -
          (this.existingImageList.length + this.imageList.length),
        success: res => {
          const processedImageList = [...this.imageList]
          if (this.resultType === 'file') {
            processedImageList.push(...res.tempFilePaths)
          } else if (this.resultType === 'base64') {
            const fileManager = uni.getFileSystemManager()
            res.tempFilePaths.forEach(v => {
              const base64 =
                'data:image/jpeg;base64,' +
                fileManager.readFileSync(v, 'base64')
              processedImageList.push(base64)
            })
          }
          this.$emit('input', processedImageList)
        },
      })
    },
    removeImage(i) {
      if (i < this.existingImageList.length) {
        this.$emit('remove-preset', i)
      } else {
        const processedImageList = [...this.imageList]
        processedImageList.splice(i - this.existingImageList.length, 1)
        this.$emit('input', processedImageList)
      }
    },
    previewImage(current) {
      uni.previewImage({
        current,
        urls: this.existingImageList.concat(this.imageList),
      })
    },
  },
}
</script>

<style lang="scss">
.image-upload {
  display: grid;
  gap: 20rpx;
  grid-template-columns: repeat(3, 1fr);

  & > view {
    border-radius: 10rpx;
    aspect-ratio: 1 / 1;
  }

  .image {
    overflow: hidden;
    position: relative;

    .close {
      display: flex;
      align-items: center;
      justify-content: center;
      position: absolute;
      top: 0;
      right: 0;
      width: 25%;
      padding: 0 0 5rpx 5rpx;
      background: rgba(0, 0, 0, 0.4);
      border-bottom-left-radius: 70%;
      aspect-ratio: 1 / 1;
    }
  }
}
</style>
