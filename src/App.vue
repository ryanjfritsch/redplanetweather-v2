<template>
  <div id="app">
    <LoadingScreen
      ref="loadingScreen"
      class="initialLoadingScreen"
      v-if="showLoadingScreen"
    />
    <DataScreen
      ref="dataScreen"
      :data="data"
      :recentSol="recentSol"
      v-if="showDataScreen"
    />
  </div>
</template>

<script>
import LoadingScreen from "./components/LoadingScreen.vue";
import DataScreen from "./components/DataScreen.vue";
import backup from "./assets/backupdata.json";

const axios = require("axios");

export default {
  name: "app",
  components: {
    LoadingScreen,
    DataScreen,
  },
  data() {
    return {
      data: null,
      backupData: backup,
      recentSol: -1,
      recentSolInd: -1,
      showLoadingScreen: true,
      showDataScreen: false,
    };
  },
  mounted: function() {
    this.$nextTick(function() {
      this.startLoadingScreen();
    });
  },
  methods: {
    startLoadingScreen() {
      setTimeout(() => {
        axios
          .get(
            "https://mars.nasa.gov/rss/api/?feed=weather&category=msl&feedtype=json"
          )
          .then((response) => {
            if (response.status == 200) {
              // console.log(response);
              let dataNasa = { ...response.data.soles[0] };
              if (dataNasa === undefined || dataNasa === null) {
                this.$refs.loadingScreen.apiLoadError(true);
              } else {
                this.data = { ...dataNasa };
                this.dataLoaded = true;
                this.recentSol = this.data.sol;
                this.apiLoadSuccess();
                this.$refs.loadingScreen.apiLoadSuccess();
              }
            } else {
              this.$refs.loadingScreen.apiLoadError();
            }
          });
      }, 6500);
    },
    apiLoadSuccess() {
      document
        .querySelector(".initialLoadingScreen")
        .classList.add("fadeOutLoadingScreen");
      setTimeout(() => {
        this.showLoadingScreen = false;
        this.showDataScreen = true;
        this.$nextTick(() => {
          this.$refs.dataScreen.startDataScreen();
        });
      }, 3000);
    },
  },
};
</script>

<style>
#app {
  width: 100vw;
  height: 100vh;
  overflow-y: scroll;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* Internet Explorer 10+ */
  background-color: black;
}
#app::-webkit-scrollbar {
  /* WebKit */
  width: 0;
  height: 0;
}

.fadeOutLoadingScreen {
  animation: loadingFadeOut 1.5s 1.3s ease-in-out reverse;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

@keyframes loadingFadeOut {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>
