<template v-if="loadingScreenShowing">
  <div>
    <div class="titleText">
      <div class="title">RED PLANET</div>
      <div class="subTitle">WEATHER</div>
    </div>

    <div class="loadingNasa">
      <div class="loadingNormalText" v-if="!errorLoadingData && !errorNoData">
        Loading weather data from&nbsp;
        <a class="nasaLink" href="https://mars.nasa.gov/rss/api/?feed=weather&category=msl&feedtype=json" target="_blank">
          <span class="nasaText">NASA</span>
        </a>
      </div>
      <div class="nasaLoader" v-if="!dataLoaded && !errorLoadingData">
        <div class="planet"></div>
      </div>
      <div class="nasaLoadSuccess" v-if="dataLoaded && !errorLoadingData && !errorNoData">
        <div class="smallLeg"></div>
        <div class="largeLeg"></div>
      </div>

      <div class="loadingNormalText" v-if="dataLoaded && errorLoadingData">
        Error retrieving data. Please refresh.
      </div>
      <div class="nasaLoadError" v-if="dataLoaded && errorLoadingData">
        <div class="xLeg"></div>
        <div class="xLeg"></div>
      </div>

      <div class="loadingNormalText" v-if="dataLoaded && errorNoData">
        <a class="nasaLink" href="https://mars.nasa.gov/insight/weather/" target="_blank">
          <span class="nasaTextNoData">NASA Mars API</span>
        </a>
        is not currently returning weather data.
      </div>
      <div class="nasaLoadError" v-if="dataLoaded && errorNoData">
        <div class="xLeg"></div>
        <div class="xLeg"></div>
      </div>
    </div>

    <div class="marsVideoContainer">
      <video loop autoplay playsinline async muted poster="../assets/marsPoster.jpg">
        <source type="video/mp4" src="../assets/mars.mp4" />
      </video>
    </div>
  </div>
</template>

<script>
export default {
  name: "LoadingPage",
  data() {
    return {
      loadingScreenShowing: true,
      dataLoaded: false,
      errorLoadingData: false,
      errorNoData: false,
    };
  },
  mounted: function() {},
  methods: {
    apiLoadSuccess() {
      this.dataLoaded = true;
    },
    apiLoadError(nasaNoData = false) {
      this.dataLoaded = true;
      if (nasaNoData) {
        this.errorNoData = true;
      } else {
        this.errorLoadingData = true;
      }
      document.querySelector(".marsVideoContainer video").classList.add("greyPlanet");
    },
  },
};
</script>

<style scoped>
.initialLoadingScreen {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow: hidden;
  background-color: black;
}
.titleText {
  z-index: 5;
  position: absolute;
  top: 5%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.title {
  font-family: nasalization, sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 40px;
  letter-spacing: 5px;
  color: white;
  animation: titleFadeIn 2s 2s ease-out reverse backwards;
}

.subTitle {
  font-family: "Rubik", sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 20px;
  letter-spacing: 2px;
  color: rgb(216, 216, 216);
  letter-spacing: 5px;
  animation: titleFadeIn 2s 2.5s ease-out reverse backwards;
}

@keyframes titleFadeIn {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

.loadingNasa {
  top: 15%;
  color: rgb(133, 133, 133);
  font-family: "Rubik", sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 14px;
  z-index: 5;
  position: absolute;
  top: 20%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  animation: titleFadeIn 1s 3.3s ease-out reverse backwards;
}

.nasaLink {
  text-decoration: none;
}

.nasaText,
.nasaTextNoData {
  font-weight: 300;
  font-style: normal;
  font-size: 14px;
  color: rgba(218, 218, 218, 0.856);
  transition: 0.2s all;
}

.nasaText {
  font-family: nasalization, sans-serif;
}

.nasaText:hover,
.nasaTextNoData:hover {
  color: white;
}

.nasaLoader {
  width: 30px;
  height: 30px;
  position: relative;
  box-sizing: border-box;
  border-radius: 15px;
  border: 3px solid rgb(87, 87, 87);
  transform-origin: center;
  animation: loaderAnimation 1s ease-in-out infinite;
}

.nasaLoader .planet {
  width: 10px;
  height: 10px;
  position: absolute;
  top: -2px;
  left: -2px;
  box-sizing: border-box;
  border-radius: 5px;
  background-color: #f0751c;
}

@keyframes loaderAnimation {
  0% {
    transform: rotate(0deg) scale(0.7);
  }
  100% {
    transform: rotate(720deg) scale(0.7);
  }
}

.nasaLoadSuccess {
  width: 30px;
  height: 30px;
  position: relative;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  border-radius: 15px;
  border: 3px solid rgb(10, 192, 4);
  transform-origin: center;
  transform: scale(0.7);
  /* animation: successAnimation 0.5s ease-out forwards; */
}

.nasaLoadSuccess .largeLeg {
  position: absolute;
  width: 4px;
  height: 14px;
  box-sizing: border-box;
  transform-origin: center;
  transform: rotate(45deg) translateX(2px) translateY(-2px);
  background-color: rgb(10, 192, 4);
}

.nasaLoadSuccess .smallLeg {
  position: absolute;
  width: 4px;
  height: 10px;
  box-sizing: border-box;
  transform-origin: center;
  transform: rotate(-45deg) translateX(-4px) translateY(-1px);
  background-color: rgb(10, 192, 4);
}

@keyframes successAnimation {
  0% {
    transform: rotate(90deg) scale(0.7);
  }
  100% {
    transform: rotate(360deg) scale(0.7);
  }
}

.loadingNormalText {
  margin-bottom: 5px;
}

.nasaLoadError {
  width: 30px;
  height: 30px;
  position: relative;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  border-radius: 15px;
  border: 3px solid rgb(241, 16, 16);
  transform-origin: center;
  transform: scale(0.7);
  animation: errorAnimation 1s ease-out;
}

.nasaLoadError .xLeg {
  position: absolute;
  width: 4px;
  height: 17px;
  box-sizing: border-box;
  transform-origin: center;
  background-color: rgb(241, 16, 16);
}

.nasaLoadError .xLeg:nth-of-type(1) {
  transform: rotate(45deg);
}

.nasaLoadError .xLeg:nth-of-type(2) {
  transform: rotate(-45deg);
}

@keyframes errorAnimation {
  0% {
    transform: rotate(0deg) scale(0.7);
  }
  100% {
    transform: rotate(360deg) scale(0.7);
  }
}

.marsVideoContainer {
  width: 100%;
  height: 100%;
  margin-left: 10px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  animation: planetZoomIn 2s 1s ease-in reverse backwards;
}

@keyframes planetZoomIn {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    /* transform: scale(2); */
  }
}

.marsVideoContainer video {
  object-fit: cover;
  height: 100%;
  width: 100%;
  -webkit-filter: saturate(1.5);
  filter: saturate(1.5);
}

.greyPlanet {
  animation: errorGreyPlanet 1.5s 0.5s ease-in-out forwards;
}

@keyframes errorGreyPlanet {
  0% {
    filter: grayscale(0) saturate(1.5) blur(0);
    opacity: 1;
  }
  100% {
    filter: grayscale(1) saturate(0) blur(8px);
    opacity: 0.5;
  }
}

@media (max-width: 700px) {
  .marsVideoContainer video {
    object-fit: contain;
  }
  .titleText {
    top: 10%;
  }
  .title {
    font-size: 30px;
  }

  .subTitle {
    font-size: 16px;
  }

  .loadingNasa {
    top: 27%;
  }
}
</style>
