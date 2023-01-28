<template>
  <div class="page">
    <div class="main">
      <img class="img" src="~/static/pikachu_dance.gif" alt="" width="100" height="100">
      <h1 class="title">MusicFrankBot</h1>
      <p class="description">by Сашка Сурин</p>
    </div>
    <button
      class="controlButton"
      @click="() => this.$router.push('/search')"
    >
      Start
    </button>
  </div>
</template>

<script>
export default {
  name: 'IndexPage'
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
  src: url('../static/font/Gilroy-Medium.ttf');
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
  height: 100vh;

  display: flex;
  align-items: center;
  justify-content: center;
}

.main {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

.title {
  margin-bottom: 8px;
}

.description {
  margin: 0;
  color: #8B8B8B;
}

.controlButton {
  position: absolute;
  bottom: 32px;
  margin-top: 16px;
  width: 70%;

  border: none;
  border-radius: 16px;
  background-color: #F7943D;
  padding: 8px;
  font-size: 20px;

  font-weight: 600;
}
</style>
