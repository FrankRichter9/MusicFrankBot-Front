<!-- Please remove this file from your project -->
<template>
  <div class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0">

    <input type="text" placeholder="search" v-model="search">
    <div class="cardsWrapper">
      <div class="card" v-for="(video, index) in searchVideosArr" :key="index">
        <div class="imgWrapper">
          <img
            :src="video.image"
            alt=""
            height="50px"
          >
        </div>
        <div class="description">
          <div>{{video.title}}</div>
          <div>{{video.timestamp}}</div>
          <div>{{video.author.name}}</div>
        </div>

        <button @click="() => selectVideo(video.url)">
          Выбрать
        </button>
        <br>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'NuxtTutorial',
  data() {
    return {
      search: '',
      searchTimeout: null,
      searchVideosArr: []
    }
  },
  watch: {
    search(newValue) {
      if (!newValue) {
        return
      }
      if (this.searchTimeout) {
        clearTimeout(this.searchTimeout)
        this.searchTimeout = null
      }
      this.searchTimeout = setTimeout(() => this.searchVideos(newValue), 1000)
    }
  },
  methods: {
   async selectVideo(url) {
      console.log('123')
      const myAxios = axios.create({
        baseURL: 'http://127.0.0.1:4000/',
        headers: {
          'Content-Type': 'application/json',
        },
      })
      myAxios.post('/', {
        url
      })
      .then((response) => {
        // handle success
        console.log(response);
        this.search = ''
        this.searchVideosArr = []
      })
    },
    searchVideos(value) {
      const myAxios = axios.create({
        baseURL: 'http://127.0.0.1:4000/',
        headers: {
          'Content-Type': 'application/json',
        },
      })
      myAxios.post('/search-videos', {
        search: value
      })
      .then((response) => {
        // handle success
        console.log(response);
        this.searchVideosArr = response.data
        console.log(this.searchVideosArr)
      })
    }
  }
}
</script>

<style scoped>
.cardsWrapper {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.card {
  border: 1px solid red;

  display: flex;
  gap: 16px;
  align-items: center;

  width: fit-content;
}

.description {
  width: 200px;
  overflow: hidden;
}

.imgWrapper {
  width: 50px;
  height: 50px;

  overflow: hidden;
}
</style>
