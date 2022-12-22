<!-- Please remove this file from your project -->
<template>
  <div class="page">
    <div class="header">
      <img src="~/static/pikachu_dance.gif" alt="" width="50" height="50">
      <h2>MusicFrankBot</h2>
    </div>
    <input type="text" class="inputSearch" placeholder="search" v-model="search">
    <div v-if="loading" class="loading">
      <p>Загрузка</p>
    </div>
    <div class="cardsWrapper" v-if="!loading">
      <div
        class="card"
        v-for="(video, index) in searchVideosArr"
        :key="index"
        @click="() => selectVideo(video.url)"
      >
        <div class="imgWrapper">
          <img
            :src="video.image"
            alt=""
            height="50px"
          >
        </div>
        <div class="description">
          <div class="title">{{video.title}}</div>
          <div>{{video.timestamp}}</div>
          <div>{{video.author.name}}</div>
        </div>
        <br>
      </div>
    </div>

    <div class="buttons">
      <button
        class="controlButton"
        @click="skipMusic"
      >
        skip
      </button>
    </div>

    <div v-if="notification" class="notification">
      {{notification}}
    </div>
  </div>
</template>

<script>
import axios from 'axios';

const myAxios = axios.create({
  baseURL: 'http://77.223.96.32:4000/',
  headers: {
    'Content-Type': 'application/json',
  },
})

export default {
  name: 'NuxtTutorial',
  data() {
    return {
      search: '',
      searchTimeout: null,
      notificationTimeout: null,
      searchVideosArr: [],
      loading: false,
      notification: ''
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
      this.searchVideosArr = []
      this.loading = true
      this.searchTimeout = setTimeout(() => this.searchVideos(newValue), 500)
    }
  },
  methods: {
   async selectVideo(url) {
      myAxios.post('/', {
        url
      })
      .then((response) => {
        this.search = ''
        this.searchVideosArr = []
        this.addNotification('add music')
      })
    },
    searchVideos(value) {
      myAxios.post('/search-videos', {
        search: value
      })
      .then((response) => {
        this.loading = false
        this.searchVideosArr = response.data
      })
    },
    skipMusic() {
      myAxios.post('/skip', {
        value: 'ok'
      })
      .then((response) => {
        this.addNotification('skip')
      })
    },
    addNotification(text) {
      this.notification = text
      if (this.notificationTimeout) {
        clearTimeout(this.notificationTimeout)
        this.notificationTimeout = null
      }
      this.notificationTimeout = setTimeout(() => this.notification = '', 1000)
    }
  }
}
</script>

<style scoped>
.header {
  display: flex;
  gap: 16px;
}

.page {
  font-family: "Sans-serif";

  max-width: 500px;
  width: 100vw;

  margin: 0 auto;
  box-sizing: border-box;
  padding: 16px;
}

.inputSearch {
  font-size: 24px;
  width: 100%;
  box-sizing: border-box;

  margin-bottom: 16px;
}

.cardsWrapper {
  display: flex;
  flex-direction: column;
  gap: 4px;

  min-height: 500px;
}

.card {
  border: 1px solid rgb(167, 167, 167);
  padding: 2px 8px;

  display: flex;
  gap: 8px;
  border-radius: 4px;
  align-items: center;

  cursor: pointer;
  transition: .3s;
}

.card:hover {
  background-color: rgb(214, 214, 214);
}

.description {
  flex: 2;
  overflow: hidden;
}

.title {
  font-size: 18px;
  font-weight: 600;
}

.imgWrapper {
  max-width: 50px;
  height: 50px;
  flex: 1;

  overflow: hidden;
}

.controlButton {
  margin-top: 16px;
  font-size: 24px;
  width: 100%;
}

.loading {
  width: 100%;
  height: 500px;

  display: flex;
  justify-content: center;
  align-items: center;

  font-size: 24px;
  font-weight: 600;
}

.notification {
  width: 100vw;
  padding: 16px 0;
  background-color: rgb(156, 255, 138);
  text-align: center;
  position: absolute;
  font-size: 30px;
  top: 50%;
  left: 0;

  font-weight: 600;
}
</style>
