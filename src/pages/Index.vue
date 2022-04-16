<template>
  <q-page class="flex column">
    <div class="col q-pa-lg">
      <q-input v-model="busca" placeholder="Busca" dark borderless>
        <template v-slot:before>
          <q-icon @click="pegarlocalizacao" name="my_location" class="cursor-pointer" />
        </template>
        <template v-slot:append>
          <q-icon name="search" class="cursor-pointer" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{ weatherData.name }}</div>

        <div class="text-h1 text-weight-light q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span>
          <span class="text-h4 relative-position degree">&deg;</span>
        </div>

        <div class="text-h6 text-weight-light">{{ weatherData.weather[0].description }}</div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" />
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar
          <br />Weather
        </div>

        <q-btn class="col" flat @click="pegarlocalizacao">
          <q-icon left size="3em" name="my_location" />
          <div>Buscar Minha Localização</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline"></div>
    <div class="footer text-grey text-subtitle2">
       FERDINANDO PRATES ©2022
    </div>
  </q-page>
</template>

<script>
export default {

  name: 'PageIndex',

  data() {
    return {
      busca: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiurl: 'https://api.openweathermap.org/data/2.5/weather?',
      apikey: 'fba5e11aa56a96d0cdb1fd118c881959'
    }
  },

  methods: {
    pegarlocalizacao() {
      //console.log('pegarlocalizacao');

      navigator.geolocation.getCurrentPosition
        (position => {
          //console.log('positio: ', position)
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
        }
        )
    },

    getWeatherByCoords() {

      this.$axios(`${this.apiurl}lat=${this.lat}&lon=${this.lon}&appid=${this.apikey}&lang=pt_br&units=metric`)
        .then(resp => {
          console.log(resp);
          this.weatherData = resp.data
        })
    },

  }

}
</script>


<style lang="sass">

.q-page
  background: linear-gradient(to bottom, #136a8a, #267871)
.degree
  top: -40px
.skyline
  flex: 0 0 110px
  background: url(../assets/skyline.png)
  background-size: contain
  background-position: center bottom
.footer
  position: absolute
  bottom: 0
  width: 100vw
  font-family: sans-serif
  font-size: 10px
  color: white
  display: flex
  align-items: center
  justify-content: center
  background: black
  opacity: 0.8
</style>