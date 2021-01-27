<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input dark borderless v-model="search" placeholder="Search" @keyup.enter="getDataByCity">
        <template v-slot:before>
          <q-icon name="my_location" @click="getDataByLocation"/>
        </template>
        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getDataByCity"/>
        </template>
      </q-input>
    </div>
    <template v-if="weatherData">
    <div class="col text-center text-white">
      <div class="text-weight-light text-h4">
        {{ weatherData.name }}
      </div>
      <div class="text-h6 text-weight-light">
        {{ weatherData.weather[0].main }}
      </div>
      <div class="q-my-md text-h1 text-weight-thin relative-position">
        <span>{{ weatherData.main.temp }}</span>
        <span class="text-h3 relative-position degree">&deg;C</span>
      </div>
    </div>
    <div class="col text-center">
      <img class = "image" :src = "`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`"/> 
    </div>
    </template>
    <template v-else>
        <div class="col text-h2 text-center text-white text-weight-thin">
          Quasar<br>Weather
        </div>
    </template>
    <div class = "bottom-image">
    </div>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lon: '',
      lat: '',
      apiPath: 'http://api.openweathermap.org/data/2.5/weather',
      apiKey: '769d973bcf00107160475a6dc979c9e6',
    };
  },
  methods: {
    getDataByCity: function() {
      this.$q.loading.show()
      this.$axios(`${ this.apiPath }?q=${ this.search }&appid=${ this.apiKey }&units=metric`)
      .then(response => {
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
    getDataByLocation: function() {
      this.$q.loading.show()
      navigator.geolocation.getCurrentPosition(position => {
        this.lon = position.coords.longitude
        this.lat = position.coords.latitude
        this.getDataByCoords()
      })
    },
    getDataByCoords: function() {
      this.$axios(`${ this.apiPath }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=metric`)
      .then(response => {
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    }
  },
  computed: {
    bgClass: function() {
      if(this.weatherData?.weather[0].icon.endsWith('n'))
        return 'bg-night'
      else if(this.weatherData?.weather[0].icon.endsWith('d'))
        return 'bg-day'
    }
  }
};
</script>
<style lang="sass">
.q-page
  background: linear-gradient(to left, #5a3f37, #2c7744)
  &.bg-day
      background: linear-gradient(to right, #2bc0e4, #eaecc6)
  &.bg-night
        background: linear-gradient(to right, #1f1c2c, #928dab)
.degree
  top : -40px
.image
  width : 100px
  height : 100px
.bottom-image
  flex: 0 0 100px
  background: url('../assets/city-5816082_960_720.png')
  background-size: contain
  background-position: center bottom
</style>
