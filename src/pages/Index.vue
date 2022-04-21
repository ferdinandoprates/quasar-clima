<template>
  <q-page class="flex column">
    <div class="col q-pa-lg">
      <q-input v-model="busca" placeholder="Digite o Local" dark borderless @keyup.enter="pegarlocalizacaoBusca()">
        <template v-slot:before>
          <q-icon @click="pegarlocalizacaoBusca" name="my_location" class="cursor-pointer" />
        </template>
        <template v-slot:append>
          <q-btn dense flat round icon="search" @click="pegarlocalizacaoBusca" />
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
          Clima
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

      pegarlocalizacaoBusca() {

        if (!this.busca) {

          this.$q.notify({
            color: "orange-8",
            textColor: "black",
            icon: "warning",
            message: "Digite um Local",
          })

        } else {
          this.weatherData = ''
          this.$axios(`${this.apiurl}q=${this.busca}&appid=${this.apikey}&lang=pt_br&units=metric`)
            .then(resp => {
              this.lat = resp.data.coord.lat
              this.lon = resp.data.coord.lon
              this.getWeatherByCoords()
              this.busca = ''
            })

            .catch(err => {
              this.busca = ''
              this.$q.notify({
                color: "orange-8",
                textColor: "black",
                icon: "warning",
                message: "Erro - Verifique o Local Digitado",
              })
            })
        }
      },

      pegarlocalizacao() {

        navigator.geolocation.getCurrentPosition
          (position => {

            this.lat = position.coords.latitude
            this.lon = position.coords.longitude
            this.getWeatherByCoords()
          }
          )
      },

      getWeatherByCoords() {

        this.$axios(`${this.apiurl}lat=${this.lat}&lon=${this.lon}&appid=${this.apikey}&lang=pt_br&units=metric`)
          .then(resp => {

            this.weatherData = resp.data
          })
      },

    }

  }
</script>



<style lang="sass">

  .q-page
    background: linear-gradient(to bottom, #304352, #d7d2cc)
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