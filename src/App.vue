<template>
  <v-app>
    <header app flat dark class="header d-flex justify-space-between">
      <v-app-bar-title>
        <h2 class="green--text">CyWeather</h2>
      </v-app-bar-title>
      <v-spacer />
      <v-btn outlined color="green">About CyWeather</v-btn>
    </header>

    <main class="main-app d-flex justify-space-around pa-11">
      <section class="form-div" style="height: 70vh">
        <h2 class="mb-10">
          Enter the longitude and Latitude to get the weather
        </h2>
        <form>
          <v-text-field
            label="Latitude"
            color="green"
            v-model="latitude"
            class="input-field"
          >
          </v-text-field>
          <v-text-field
            label="Longitude"
            color="green"
            v-model="longitude"
            class="input-field green--text"
          >
          </v-text-field>
          <v-btn
            depressed
            outlined
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

      <section
        class="weather-data mb-11"
        v-if="alignedData.length > 0"
        style="width: 50%"
      >
        <div class="section-title text-center white--text mb-5">
          <h2>Today's Weather Forecast</h2>
        </div>

        <v-layout row wrap class="weather-cards">
          <div
            width="300px"
            class="weather-div text-center pa-1 d-flex justify-space-between"
            v-for="(data, i) in alignedData"
            :key="i"
          >
            <div class="shown-details">
              <p>{{ data.time | dateFilter }}</p>
              <img
                width="100px"
                :src="require('./assets/weather.jpg')"
                alt="weather-icon"
              />
              <p class="mt-2">{{ data.temperature }} &#8451;</p>
            </div>

            <div class="separator" style="width: 100px" />

            <div class="more-details d-flex align-center">
              <v-list color="transparent">
                <v-list-item>
                  <v-list-item-content class="white--text">
                    Windspeed: {{ data.windspeed }} km/h
                  </v-list-item-content>
                </v-list-item>
                <v-divider></v-divider>
                <v-list-item>
                  <v-list-item-content class="white--text">
                    Humidity levels: {{ data.humidity }} %
                  </v-list-item-content>
                </v-list-item>
                <v-divider></v-divider>
                <v-list-item>
                  <v-list-item-content class="white--text">
                    Cloud Cover: {{ data.cloudcover }} %
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </div>
          </div>
        </v-layout>
      </section>
    </main>
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

      hourlyWeatherData: null,
      alignedData: [],
    };
  },

  filters: {
    dateFilter(val) {
      return moment(String(val)).format("h A");
    },
  },

  methods: {
    async getWeatherData() {
      try {
        if (this.latitude === "" && this.longitude === "") {
          alert("The latitude and longitude must be filled!");
        } else {
          const resp = await this.$http.get(
            `/v1/forecast?latitude=${this.latitude}&longitude=${this.longitude}&hourly=temperature_2m,relativehumidity_2m,cloudcover,windspeed_120m`
          );
          this.hourlyWeatherData = resp.data.hourly;
          this.showOnlyTodaysWeatherData();
        }
      } catch (e) {
        console.log(e.message);
      }
    },

    showOnlyTodaysWeatherData() {
      let today = new Date().getDate();

      this.hourlyWeatherData.time.map((time, i) => {
        let timeRange = new Date(time).getHours();
        let timeDate = new Date(time).getDate();
        if (timeRange >= 6 && timeRange <= 18 && timeDate === today) {
          this.alignedData.push({
            time: time,
            humidity: this.hourlyWeatherData.relativehumidity_2m[i],
            windspeed: this.hourlyWeatherData.windspeed_120m[i],
            temperature: this.hourlyWeatherData.temperature_2m[i],
            cloudcover: this.hourlyWeatherData.cloudcover[i],
          });
        }
      });
    },
  },
};
</script>

<style lang="sass" scoped>
.header
  height: 70px
  display: flex
  justify-content: space-between
  align-items: center
  padding: 0 16px
  background-color: rgba(0, 0, 0, 0.7)

.main-app
  background-image: url("./assets/weather-bck.jpg")
  background-size: cover
  height: 100vh

.form-div
  width: 40%
  border: 1px solid grey
  background-color: rgba(0, 0, 0, 0.3)
  color: white
  padding: 3rem
  border-radius: 10px

.weather-data
  overflow-y: scroll
  overflow-x: hidden
  -ms-overflow-style: none
  scrollbar-width: none

.weather-data::-webkit-scrollbar
  display: none

.weather-div
  background-color: rgba(208, 197, 203, 0.2)
  margin: 6px
  color: white

.input-field
  color: white
</style>
