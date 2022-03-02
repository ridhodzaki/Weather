<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        v-model="search"
        @keyup.enter="getWeatherBySearch"
        placeholder="Search"
        dark
        borderless
        >
        <template v-slot:before>
          <q-icon
          @click="getLocation"
            name="my_location" />
        </template>

        <template v-slot:append>
          <q-btn
          @click="getWeatherBySearch"
            round
            dense
            flat
            icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{ weatherData.name }}
        </div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin
        q-my-lg">
        <span>{{ Math.round(weatherData.main.temp) }}</span>
        <span class="text-h4 relative-position degree">&deg;</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`">
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Weather<br>
          <div class="col text-h5 text-weight-bold">
          By Hayyah Team
          </div>
        </div>
        <q-btn
          @click="getLocation"
          class="col"
          flat>
          <q-icon left size="3em" name="my_location" />
          <div>Find My Location</div>
        </q-btn>
      </div>
    </template>

    <div class="col skyline">
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '1253566b6e770b5f69d2ea8b3db9e78d'
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night'
        } else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()
      // console.log('getLocation')
      navigator.geolocation.getCurrentPosition
      (position => {
        // console.log('position: ',position)
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
      })
    },
    getWeatherByCoords() {
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }&units=metric`)
      .then(response => {
        console.log('response: ', response)
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
    getWeatherBySearch() {
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?q=${ this.search }&appid=${ this.apiKey }&units=metric`)
      .then(response => {
        console.log('response: ', response)
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to bottom,#136a8a, #267871)
    &.bg-night
     background: linear-gradient(to bottom,#232526, #414345)
    &.bg-day
      background: linear-gradient(to bottom,#00b4db, #0083b0)
  .degree
    top: -44px
  .skyline
    flex: 0 0 70px
    background: url(../assets/skyline.png)
    background-size: contain
    background-position: center bottom
</style>
