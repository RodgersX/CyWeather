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

      <section class="mb-11">
        <div class="section-title text-center mb-5">
          <h2>Today's Weather forecast</h2>
        </div>
        <div class="d-flex weather-cards">
          <v-card
            tabindex="1"
            color="green lighten-4"
            class="weather-div text-center pa-1 d-flex justify-space-between"
            v-for="(data, i) in alignedDataFromAllLists"
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

            <div class="separator" style="width: 100px"></div>

            <div class="hidden-details">
              <v-list>
                <v-list-item>
                  <v-list-item-content>
                    Windspeed: {{ data.windspeed }} km/h
                  </v-list-item-content>
                </v-list-item>
                <v-divider></v-divider>
                <v-list-item>
                  <v-list-item-content>
                    Humidity levels: {{ data.humidity }} %
                  </v-list-item-content>
                </v-list-item>
                <v-divider></v-divider>
                <v-list-item>
                  <v-list-item-content>
                    Cloud Cover: {{ data.cloudcover }} %
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </div>
          </v-card>
        </div>
        <div class="more-details"></div>
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

      hourlyWeatherData: null,
      hourlyUnits: null,
    };
  },

  filters: {
    dateFilter(val) {
      return moment(String(val)).format("h A");
    },
  },

  computed: {
    alignedDataFromAllLists() {
      return this.hourlyWeatherData.time.map((time, i) => {
        return {
          time: time,
          temperature: this.hourlyWeatherData.temperature_2m[i],
          humidity: this.hourlyWeatherData.relativehumidity_2m[i],
          windspeed: this.hourlyWeatherData.windspeed_120m[i],
          cloudcover: this.hourlyWeatherData.cloudcover[i],
        };
      });
    },
  },

  created() {
    const resp = this.$http
      .get(
        `/v1/forecast?latitude=52.52&longitude=12.45&hourly=temperature_2m,relativehumidity_2m,cloudcover,windspeed_120m`
      )
      .then((result) => {
        this.hourlyWeatherData = result.data.hourly;
      })
      .catch((err) => console.log(err.message));
  },

  methods: {
    async getWeatherData() {
      const resp = await this.$http.get(
        `/v1/forecast?latitude=${this.latitude}&longitude=${this.longitude}&hourly=temperature_2m,relativehumidity_2m,cloudcover,windspeed_120m`
      );
      // this.hourlyWeatherData = resp.data.hourly;
      // this.hourlyUnits = resp.data.hourly_units;
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
  width: 95%
  margin: 1rem auto
  display: flex

.weather-cards
  overflow-x: scroll

.v-card
  margin: 0 10px

.v-card:focus
  width: 200px
</style>
