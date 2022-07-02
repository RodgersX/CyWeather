<template>
  <v-app>
    <v-app-bar app color="primary" dark> </v-app-bar>

    <v-main>
      {{ weather }}
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      weather: null,
      temp: null,
      wind: null,
      cloudCover: null,
    };
  },

  async created() {
    const resp = await this.$http.get(
      "/v1/forecast?latitude=50.52&longitude=13.41&hourly=temperature_2m,relativehumidity_2m,cloudcover,windspeed_120m"
    );
    this.weather = resp.data;
  },
};
</script>
