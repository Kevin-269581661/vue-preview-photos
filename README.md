# vue-preview-photos
一个基于Element的el-carousel预览图片的二次封装组件，有显示略缩图、可单张、多张预览，拖拽、放大等功能<br/>
因为项目需要使用到点击略缩图预览的功能，找了一下vue的图片预览组件，没有达到项目的需求，所以自己封装了一个预览的组件，因为项目使用的是element-ui,所以借助了\<el-carousel>来实现轮播切换的效果。<br/>
## 使用方法：
* 首先引入element-ui，因为使用了element组件<br/>
* 下载previewPhoto.vue文件（可以直接copy代码），放入components文件夹下，在需要使用的组件内引入：<br/>
  ```javascript
  import PreviewPhoto from "@/components/previewphoto"
  ```
  注册组件：
  ```javascript
  export default {
    components: { PreviewPhoto },
    data() {
      retrun { 
        previewPictureArray: []  // 包含略缩图路径和原图路径的对象数组
        /**
        * 数组的要求格式为：
        * [
        *   {
        *     fullThumbnailPath: 'http://cdn.duitang.com/uploads/item/201501.png',
        *     fullPath: 'http://cdn.duitang.com/uploads/item/201501.thumb.png'
        *   },
        *   ...
        * ]
        */
      }
    }
    // ...
  }
  ```
  HTML:
  ```html
  <template>  
    <div>
    <!-- ... -->
    <img src="http://cdn.duitang.com/uploads/item/201501/08/20150108164231_t432j.thumb.700_0.png" @click="openPreviewPhoto" />
    <!-- 图片预览组件 -->
    <PreviewPhoto ref="preview" :pictureArray="previewPictureArray" ></PreviewPhoto>
    </div>
  </template>
  ```
  方法调用：
  ```javascript
  // 打开预览组件并设置选中图片（默认为第一个）
  this.$refs.preview.setDialogTableVisible();
  this.$refs.preview.handleSetActiveItem(index);
  ```
关于封装还有很多要学的，有很多不足还需要去改进... 