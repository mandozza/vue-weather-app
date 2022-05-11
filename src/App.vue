<template>
<div id="#app">
  <main>
    <div class="welcome">
      {{ getWelcomeMessage }}
    </div>
    <div class="search-box">
      <input type="text" class="search-bar" v-model="search" @keyup.enter="getWeather" placeholder="Enter a City!">
    </div>
    <div v-if="messaging">{{messaging}}</div>
    <div v-if="temp">
      <div class="weather-results">
        <div class="city"> {{ this.city }} </div>
        <div class="temp">{{ temp }}</div>
      </div>
    </div>
  </main>
</div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      apiKey: process.env.VUE_APP_API_KEY,
      apiUrl: process.env.VUE_APP_API_URL,
      apiGeoUrl: process.env.VUE_APP_API_GEO_URL,
      lat: null,
      long: null,
      search: '',
      messaging: null,
      city: null,
      temp: null,
    }
  },
  methods: {
    async getWeather () {
      this.messaging = ''
      this.city = this.search
      this.search = ''

      const response = await fetch(`${this.apiGeoUrl}direct?q=${this.city}&limit=1&appid=${this.apiKey}`)
      const city = await response.json()
      this.lat = city[0].lat
      this.long = city[0].lon

      if (!this.lat && !this.long) {
        this.messaging = `Sorry, we couldn't find ${this.city}. Please try a different city.`
      } else {
        const res = await fetch(`${this.apiUrl}weather?lat=${this.lat}&lon=${this.long}&APPID=${this.apiKey}`)
        const weatherResults = await res.json()
        this.temp = weatherResults.main.temp
        this.lat = null
        this.long = null
        this.messaging = null
      }
    },
  },
  computed: {
    getWelcomeMessage() {
      const dayOfWeekName = new Date().toLocaleString('default', {weekday: 'long'} )
      return `Happy ${dayOfWeekName}!!`;
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background: #026773;
  color: #F2E3D5;
  position: absolute;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
}
main {
  padding-top: 80px;
}
.welcome {
  font-size: 2em;
  padding-bottom: 20px
}
.city {
  font-size: 3rem;
  font-weight: bold;
  text-transform: capitalize;
}

.search-bar{
  border: 1px solid #024959;
  border-radius: 5px;
  padding: 8px;
  background-color: #012e40;
  color:#F2E3D5;
  margin-bottom: 30px;
  width: 250px;
}

</style>
