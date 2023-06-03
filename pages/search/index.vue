<template>
  <div class="page">
    <div class="header">
      <img class="img" src="~/static/pikachu_dance.gif" alt="" width="50" height="50">
      <div class="headerRight">
        <h2>MusicFrankBot</h2>
        <img
          class="likesImg"
          src="../../static/like-106.png"
          alt=""
          width="25"
          height="25"
          @click="(e) => this.$router.push('favorites')"
        >
      </div>
    </div>
    <input
      type="text"
      class="inputSearch"
      placeholder="search"
      v-model="search"
    >
    <div v-if="loading" class="loading">
      <Loader />
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
            height="60px"
          >
        </div>
        <div class="description">
          <div class="title">{{video.title}}</div>
          <div class="timestampAuthorWrapper">
            <div class="author">{{video.author.name}}</div>
            <div>{{video.timestamp}}</div>
          </div>
        </div>
        <img
          class="likeImg"
          src="../../static/like-106.png"
          alt=""
          width="25"
          height="25"
          @click="(e) => saveVideo(e, video)"
        >
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
  baseURL: 'http://82.148.30.218:4000/',
  headers: {
    'Content-Type': 'application/json',
  },
})

export default {
  name: 'SearchPage',
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
      if (this.searchTimeout) {
        clearTimeout(this.searchTimeout)
        this.searchTimeout = null
      }
      if (!newValue.trim()) {
        this.loading = false
        return
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
    },
    saveVideo(e, video) {
      e.stopPropagation()
      this.addNotification('Added to favorites')
      const favoritesStr = localStorage.getItem('favorites') || '[]'
      let favorites = []
      try {
        favorites = JSON.parse(favoritesStr)
      } catch {
        this.addNotification('error added to favorites')
        return
      }
      favorites.push(video)
      try {
        const str = favorites = JSON.stringify(favorites)
        localStorage.setItem('favorites', str)
      } catch {
        this.addNotification('error added to favorites')
        return
      }
      console.log(video)
    }
  }
}
</script>

<style>
body {
  margin: 0 !important;
}
</style>

<style scoped>
@font-face {
  font-family: "Gilroy-Medium";
  src: url('../../static/font/Gilroy-Medium.ttf');
}

.header {
  display: flex;
  gap: 16px;
}

.headerRight {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 80%;
}

.page {
  font-family: "Gilroy-Medium";

  max-width: 500px;
  width: 100vw;

  margin: 0 auto;
  box-sizing: border-box;
  padding: 16px;

  background-color: #0D0D0D;
  color: #F9F9F9;

  min-height: 100vh;
}

.inputSearch {
  font-size: 24px;
  width: 100%;
  box-sizing: border-box;

  margin-bottom: 16px;

  background-color: transparent;
  color: #F9F9F9;
  border: 1px solid #8B8B8B;

  padding: 8px 24px;
  border-radius: 16px;
}

.cardsWrapper {
  display: flex;
  flex-direction: column;
  gap: 8px;

  min-height: 400px;
}

.card {
  border: 1px solid #8B8B8B;
  padding: 4px 8px;

  display: flex;
  gap: 8px;
  border-radius: 16px;
  align-items: center;

  cursor: pointer;
  transition: .3s;
}

.card:hover {
  background-color: rgb(214, 214, 214);
}

.description {
  font-size: 12px;

  flex: 2;
  overflow: hidden;
}

.author {
  max-width: 140px;
  white-space: nowrap;

  overflow: hidden;
  text-overflow: ellipsis;
}

.timestampAuthorWrapper {
  display: flex;
  gap: 16px;

  color: #8B8B8B;
}

.title {
  font-size: 16px;
  font-weight: 600;

  max-height: 40px;
  overflow: hidden;
  text-overflow: ellipsis;
}

.imgWrapper {
  max-width: 60px;
  height: 60px;
  flex: 1;

  overflow: hidden;

  border-radius: 50%;
  box-shadow: 0px 0px 8px 0px rgba(0, 0, 0, 0.68);
}

.likeImg {
  filter: invert(1);
  opacity: .5;
}

.likesImg {
  filter: invert(1);
}

.controlButton {
  margin-top: 16px;
  width: 100%;

  border: none;
  border-radius: 16px;
  background-color: #F7943D;
  padding: 8px;
  font-size: 20px;

  font-weight: 600;
}

.loading {
  width: 100%;
  height: 400px;

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
