<template>
  <div>
    <div class="tab-bar">
      <div
        v-for="folder in folders"
        :key="folder.name"
        :class="{ 'tab-item': true, active: folder === selectedFolder }"
        @click="showImages(folder)"
      >
        {{ folder.name }}
      </div>
    </div>

    <div v-if="selectedFolder" class="image-container">
      <div v-for="url in selectedFolder.urls" :key="url" class="image-wrapper">
        <img :src="url" alt="" class="imgs" @click="enlargeImage(url)" />
      </div>
    </div>

    <div v-if="enlargedImage" class="enlarged-image-overlay" @click="closeEnlargedImage">
      <img :src="enlargedImage" alt="" class="enlarged-image" />
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      folders: [],
      selectedFolder: null,
      enlargedImage: null, // 新增的数据属性，用于存储点击的图片 URL
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    // fetchData() {
    //   // 模拟 API 调用
    //   this.folders = [
    //     {
    //       name: "相册1",
    //       urls: ["https://djjdjj.s3.us-west-002.backblazeb2.com/photo/youzimaoj/02/01.jpg", "https://djjdjj.s3.us-west-002.backblazeb2.com/photo/youzimaoj/02/02.jpg"]
    //     },
    //     {
    //       name: "相册2",
    //       urls: ["https://djjdjj.s3.us-west-002.backblazeb2.com/photo/youzimaoj/02/03.jpg", "https://djjdjj.s3.us-west-002.backblazeb2.com/photo/youzimaoj/02/04.jpg"]
    //     }
    //   ];
    // },
    async fetchData() {
      try {
        const response = await axios.get('https://api.vder.cn/footprint/images'); // 替换成实际的 API 请求地址
        //this.folders = response.data;
        const encryptedData = response.data.data;
        const encodedData = atob(encryptedData);
        const decryptedData = JSON.parse(encodedData);
        this.folders = decryptedData;
      } catch (error) {
        console.error(error);
      }
    },
    showImages(folder) {
      this.selectedFolder = folder;
    },
    enlargeImage(url) {
      this.enlargedImage = url; // 将点击的图片 URL 设置为 enlargedImage 属性的值
    },
    closeEnlargedImage() {
      this.enlargedImage = null; // 将 enlargedImage 属性重置为 null，关闭放大的图片
    },
  },
};
</script>
<style>
.tab-bar {
  display: flex;
  justify-content: center;
  align-items: center;
}

.tab-item {
  padding: 10px;
  cursor: pointer;
}

.tab-item.active {
  background-color: lightgray;
}

.selected-button {
  margin-left: 10px;
}

.image-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.image-wrapper {
  margin: 10px;
}

.imgs {
  width: 200px;
  height: auto;
}

.enlarged-image-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.enlarged-image {
  max-width: 100%;
  max-height: 100%;
}
</style>