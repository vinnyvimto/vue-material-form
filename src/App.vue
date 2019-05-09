<template>
  <v-app id="inspire">
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex sm12 md8>
            <v-card>
              <v-toolbar flat card dense>
                <v-toolbar-title></v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <ServiceList @continued="showJson" />
              </v-card-text>
            </v-card>

            <pre v-if="json" v-text="json"></pre>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import ServiceList from "./components/ServiceList.vue";

export default {
  name: "app",
  components: {
    ServiceList
  },

  data() {
    return {
      checkbox: false,
      json: null
    };
  },

  methods: {
    showJson(forms) {
      this.json = JSON.stringify(forms, null, 2);
    }
  }
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
