<template>
  <div class="page">
    <div class="cardsWrapper">
      <div
          class="card"
          v-for="(video, index) in music"
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
            class="delImg"
            src="../../static/delete-icon.svg"
            alt=""
            width="25"
            height="25"
            @click="(e) => deleteMusic(e, video)"
          >
          <br>
        </div>
       </div>
       <button
        class="controlButton"
        @click="() => this.$router.push('/search')"
      >
        back
      </button>
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
  name: 'FavoritesPage',
  data() {
    return {
      music: [],
      notification: ''
    }
  },
  mounted() {
    this.updateData()
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
    addNotification(text) {
      this.notification = text
      if (this.notificationTimeout) {
        clearTimeout(this.notificationTimeout)
        this.notificationTimeout = null
      }
      this.notificationTimeout = setTimeout(() => this.notification = '', 1000)
    },
    deleteMusic(e, video) {
      e.stopPropagation()
      const url = video.url
      const favoritesStr = localStorage.getItem('favorites') || '[]'
      let favorites = []
      try {
        favorites = JSON.parse(favoritesStr)
      } catch {
        this.addNotification('error delete to favorites')
        return
      }
      favorites = favorites.filter(m => m.url !== url)
      try {
        const str = favorites = JSON.stringify(favorites)
        localStorage.setItem('favorites', str)
      } catch {
        this.addNotification('error delete to favorites')
        return
      }
      this.updateData()
    },
    updateData() {
      const videos = localStorage.getItem('favorites') || '[]'
      try {
        this.music = JSON.parse(videos)
      } catch {
        console.log('err')
      }
    }
  }
}
</script>

<style>
@font-face {
  font-family: "Gilroy-Medium";
  src: url('../../static/font/Gilroy-Medium.ttf');
}

body {
  margin: 0 !important;
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

.cardsWrapper {
  display: flex;
  flex-direction: column;
  gap: 8px;

  min-height: 500px;
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

.delImg {
  filter: invert(1);
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

</style>
