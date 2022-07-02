<template>
  <v-app>
    <v-app-bar app color="green" dark>
      <v-app-bar-title>
        <h2>CyWeather</h2>
      </v-app-bar-title>
    </v-app-bar>

    <v-main>
      <section class="form-div">
        <h2 class="mb-10">
          Enter the longitude and Latitude to get the weather
        </h2>
        <form>
          <v-text-field label="Latitude" color="green" v-model="latitude">
          </v-text-field>
          <v-text-field label="Longitude" color="green" v-model="longitude">
          </v-text-field>
          <v-btn
            @click.prevent="getWeatherData"
            @keyup.enter="getWeatherData"
            block
            color="green darken-2"
            class="mt-6 white--text"
          >
            Submit
          </v-btn>
        </form>
      </section>

      <section class="weather-data">
        <v-list class="">
          <v-list-item v-for="(item, i) in weather" :key="i">
            <v-list-item-content>
              <div class="d-flex align-center">
                <h4>1PM</h4>
                <h5 class="ml-4">21 &#8451;</h5>
              </div>
            </v-list-item-content>
            <v-list-item-content>
              <p class="grey--text">RealFeel &copy;</p>
            </v-list-item-content>
            <v-list-item-content>
              <div class="d-flex align-start grey--text">
                <i class="bx bx-droplet"></i>
                <p class="mx-1">0%</p>
                <v-btn icon>
                  <i class="bx bx-chevron-down bx-sm"></i>
                </v-btn>
              </div>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <div class="sample-test">
          
        </div>
      </section>
    </v-main>
  </v-app>
</template>

<script>
import moment from "moment";

export default {
  name: "App",

  data() {
    return {
      latitude: "",
      longitude: "",

      weather: null,
      temp: null,
      wind: null,
      cloudCover: null,
    };
  },

  filters: {
    dateFilter(val) {
      return moment(String(val)).format("h A");
    },
  },

  methods: {
    async getWeatherData() {
      const resp = await this.$http.get(
        `/v1/forecast?latitude=${this.latitude}&longitude=${this.longitude}&hourly=temperature_2m,relativehumidity_2m,cloudcover,windspeed_120m`
      );
      this.weather = resp.data;
    },
  },
};
</script>

<style lang="sass" scoped>
.form-div
  width: 50%
  margin: 4rem auto
  border: 1px solid grey
  padding: 3rem
  border-radius: 10px

.weather-data
  background-color: red
  width: 70%
  margin: 1rem auto
</style>
