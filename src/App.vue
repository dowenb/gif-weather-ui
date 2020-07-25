<template>
  <div id="app">
      <loading :active.sync="isLoading" 
      :can-cancel="true" 
      :on-cancel="onCancel"
      :is-full-page="fullPage">
    </loading>
    <img src="gif-weather.png" alt="GIF Weather">
    <input v-model="searchTerm" type="text" v-on:keyup.enter="getGifs()" placeholder="Enter a location.">
    <button class="button" @click=getGifs()>Get GIF Weather</button>
    <div class="gif-container">
      <img :src="gif" :alt="title" @load="gifLoaded">
      <p> {{ title }} </p>
    </div>
  </div>
</template>

<script>
// Import vue-loading-overlay and style
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  data() {
    return {
      isLoading: false,
      fullPage: true,
      searchTerm: "",
      gif: "",
      title: ""
    };
  },
  components: {
    Loading
  },
  methods: {
    getGifs() {
      this.isLoading = true;
      let endpoint = "https://gif-weather.herokuapp.com";
      let url = `${endpoint}/${
        this.searchTerm
      }`;
      fetch(url)
        .then(response => {
          return response.json();
        })
        .then(json => {
          this.buildGifs(json);
        })
        .catch(err => {
          this.isLoading = false;
          console.log('Cannot find weather for ' + this.searchTerm);
          console.log(err);
          this.handleError();
        });
    },
    buildGifs(json) {
      this.title = json.data[0].title;
      this.gif = `https://media.giphy.com/media/${json.data[0].id}/giphy.gif`;
    },
    handleError(){
      this.title = 'Cannot find weather for ' + this.searchTerm;
      this.gif = 'weather-not-found.png';
      this.isLoading = false;
    },
    onCancel() {
      console.log('User cancelled the loader.')
    },
    gifLoaded(){
      this.isLoading = false;
    }
  }
};
</script>

<style>
body{
  text-align: center;
}
#app {
  max-width: 653px;
  display: inline-block;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
input {
  width: 80%;
  padding: 5px;
  margin-bottom: 10px;
}
.button {
  background-color: rgb(0, 172, 0);
  color: white;
  padding: 5px 20px;
  border: none;
  display: block;
  margin: 0 auto;
}
.button:hover {
  background-color: rgb(0, 148, 0);
}
.gif-container {
  margin-top: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
img {
  max-width: 100%;
  height: auto;
}
</style>