<template>
  <div class="container">
    <ul>
      <li v-for="(item, i) in previewPictureArray" :key="i">
        <img :src="item.fullThumbnailPath" @click.stop="handlePreviewPhoto(i)">
        <!-- <img src="../assets/thumbnail-img01.jpg" alt=""> -->
      </li>
    </ul>
    <!-- 预览组件 -->
    <preview-photo ref="PreviewPhoto" :previewPictureArray="previewPictureArray"></preview-photo>
  </div>
</template>
<script>
import PreviewPhoto from './previewPhoto.vue'

export default {
  name: 'demo',
  components: {
    PreviewPhoto
  },
  data() {
    return {
      test: require('../assets/thumbnail-img01.jpg'),
      previewPictureArray: [
        {
          fullThumbnailPath: require('../assets/thumbnail-img01.jpg'),
          fullPath: require('../assets/full-img01.jpg')
        },
        {
          fullThumbnailPath: require('../assets/thumbnail-img02.jpg'),
          fullPath: require('../assets/full-img02.jpg')
        },
        {
          fullThumbnailPath: require('../assets/thumbnail-img03.jpg'),
          fullPath: require('../assets/full-img03.jpg')
        }
      ]
    }
  },
  methods: {
    handlePreviewPhoto(index) {
      this.$refs.PreviewPhoto.handleSetActiveItem(index);
    }
  },
}
</script>
<style lang="stylus">
.container {
  ul {
    display flex
    li {
      width 200px
      height 110px
      margin-right 50px
      overflow hidden

      img {
        width 100%
      }
    }
  }
  
}
</style>