<template>
  <main id="app">
    <header>
      <h1>Vue Gifs <i class="fas fa-th-large"></i></h1>

      <div class="banner">
        <div>
          <p v-if="!noGifs">Currently Viewing: <span v-if="!current">trending</span> <span v-else>{{ current }}</span> gifs</p>
        </div>

        <form v-on:submit.prevent="getQueryGifs">
          <input v-bind:class="{bigInput: searching}" type="text" name="query" placeholer="cats" v-model="query" v-on:blur="toggleInputSize" v-on:focus="toggleInputSize"/>
          <button><i class="fas fa-search"></i></button>
        </form>
      </div>
    </header>

    <ul v-if="!noGifs">
      <li v-for="(gif, index) in gifs" :key="index">
        <img v-bind="{src: gif.images.downsized_medium.url}" />
      </li>
    </ul>

    <div v-if="noGifs" class="error">
      <i class="fas fa-times-circle"></i>
      <p>Oh no, seems like there are no gifs that match your search</p>
    </div>
  </main>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      gifs: [],
      query: '',
      current: '',
      noGifs: false,
      searching: false
    }
  },
  created () {
    this.getTredningGifs()
  },
  methods: {
    getTredningGifs () {
      this.current = ''

      axios
        .get('http://api.giphy.com/v1/gifs/trending?api_key=V71aYiBc9MNocrdCDU9dv5uhOh0sn2FT&limit=9')
        .then(this.handleGifData)
    },
    getQueryGifs () {
      if (this.query.length > 0) {
        axios
          .get(`http://api.giphy.com/v1/gifs/search?api_key=V71aYiBc9MNocrdCDU9dv5uhOh0sn2FT&limit=9&q=${this.query}`)
          .then(this.handleGifData)
      }
    },
    handleGifData (res) {
      const { data: { data } } = res

      if (data.length > 0) {
        this.current = this.query
        this.gifs = data
      } else {
        this.noGifs = true
      }
    },
    toggleInputSize () {
      this.searching = !this.searching
    }
  },
  watch: {
    query () {
      if (!this.query) {
        this.noGifs = false
        this.getTredningGifs()
      }
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Poppins:300,500,700,900');

#app {
  font-family: 'Poppins', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #333;
  margin: 0 auto;
  max-width: 930px;
}

h1 {
  text-align: left;
  font-size: 70px;
  margin: 0;
}

p {
  margin: 0;
  font-size: 20px;
}

p span {
  font-weight: bold;
  font-size: 26px;
}

img {
  width: 100%;
  height: 100%;
  border-radius: 3px;
}

button {
  border: none;
  font-size: 20px;
  color: #333;
}

input {
  font-size: 18px;
  background: #fafafa;
  border: 3px solid #eee;
  border-radius: 3px;
  padding: 3px;
  font-family: 'Poppins', sans-serif;
  transition: all 0.1s;
  outline: none;
}

ul {
  clear: both;
  margin: 5px auto;
  padding: 0;
  list-style: none;
  max-width: 930px;
}

li {
  float: left;
  width: 300px;
  height: 300px;
  margin: 5px;
}

.bigInput {
  font-size: 25px;
}

.banner {
  height: 60px;
  line-height: 60px;
  margin-top: -20px;
  padding: 0 15px;
}

.banner div {
  float: left;
}

.banner form {
  float: right;
}

.error {
  margin: 60px auto;
}

.error i {
  font-size: 100px;
  color: #AA3939;
}

.error p {
  margin: 10px;
  font-size: 25px;
}
</style>
