<template>
  <div class="dataScreen" v-if="dataScreenShowing">
    <div class="tempDataContainer">
      <div class="titleContainerLarge">
        <div class="title">RED PLANET</div>
        <div class="subTitle">WEATHER</div>
      </div>
      <div class="tempData" v-if="data['AT'] == null">
        <div class="titleContainerSmall">
          <div class="title">RED PLANET</div>
          <div class="subTitle">WEATHER</div>
        </div>
        <div class="row titleRow">
          <div class="label">Most Recent Date</div>
          <div class="earthDate">{{ recentTempTimeStamp }}</div>
          <div class="solTitle">Sol {{ recentSol }}</div>
          <div class="planetTitle">Mars</div>
        </div>
        <div class="row tempRow">
          <div class="recentTempData">
            <div class="label">Avg. Temp</div>
            <div class="data">{{ avgTemp }}</div>
          </div>
          <div class="recentWindData">
            <div class="label">Avg. Wind Speed</div>
            <div class="data">{{ avgWindSpeed }}</div>
          </div>
        </div>
        <div class="row tempRange">
          <div class="label">Daily Air Temp Range</div>
          <div class="rangeTemps">
            <div class="data lowTemp">{{ parseInt(data["min_temp"] * (9 / 5)) + 32 }}°F</div>
            <div class="data highTemp">{{ parseInt(data["max_temp"] * (9 / 5)) + 32 }}°F</div>
          </div>
          <div class="colorBar"></div>
        </div>
        <div class="row tempRange">
          <div class="label">Daily Ground Temp Range</div>
          <div class="rangeTemps">
            <div class="data lowTemp">{{ parseInt(data["min_gts_temp"] * (9 / 5)) + 32 }}°F</div>
            <div class="data highTemp">{{ parseInt(data["max_gts_temp"] * (9 / 5)) + 32 }}°F</div>
          </div>
          <div class="colorBar"></div>
        </div>
        <div class="row nasaRow">
          <a href="https://mars.nasa.gov/insight/weather/" target="_blank" class="textRow">
            Data provided by
            <span class="nasaText">NASA</span> Insight Weather API
          </a>
        </div>
      </div>

      <div class="no-temp-data" v-if="data['AT'] != null">
        <div class="titleContainerSmall">
          <div class="title">RED PLANET</div>
          <div class="subTitle">WEATHER</div>
        </div>
        <div class="row tempRow">
          <div class="recentTempData">
            <div class="label">
              InSight has temporarily suspended daily temperature measurements. As more data becomes available, it will appear here.
            </div>
          </div>
        </div>
        <div class="row nasaRow noDataNasaRow">
          <a href="https://mars.nasa.gov/rss/api/?feed=weather&category=msl&feedtype=json" target="_blank" class="textRow">
            <span class="nasaText">NASA</span> Insight Weather API
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DataScreen",
  data() {
    return {
      dataScreenShowing: false,
      months: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
      recentTempTimeStamp: "",
      avgTemp: "--",
      avgWindSpeed: "--",
    };
  },
  props: ["data", "recentSol"],
  mounted: function () {
    this.$nextTick(function () {});
  },
  methods: {
    startDataScreen() {
      this.dataScreenShowing = true;
      setTimeout(() => {
        document.querySelector(".dataScreen").classList.add("dataScreenReady");
        document.querySelector(".titleContainerLarge").classList.add("dataContainersReady");

        if (document.querySelector(".tempData")) {
          document.querySelector(".tempData").classList.add("dataContainersReady");
        } else if (document.querySelector(".no-temp-data")) {
          document.querySelector(".no-temp-data").classList.add("dataContainersReady");
        }

        try {
          this.avgTemp =
            Math.floor((parseInt(this.data["min_temp"]) * (9 / 5) + parseInt(this.data["max_temp"]) * (9 / 5)) / 2 + 32) + "°F";
        } catch (err) {
          // eslint-disable-next-line no-console
          console.error("Error calculating avgTemp: ", err);
        }

        this.avgWindSpeed = this.data["wind_speed"] + (this.data["wind_speed"].includes("-") ? "" : " MPH");

        this.formatRecentDate();
      }, 300);
    },
    formatRecentDate() {
      let dateStrings = this.data["terrestrial_date"].split("-");
      let month = this.months[parseInt(dateStrings[1]) - 1];
      let day = parseInt(dateStrings[2]);
      let year = parseInt(dateStrings[0]);
      this.recentTempTimeStamp = `${month.slice(0, 3)} ${day}, ${year}`;
    },
    setDataPoints() {},
  },
};
</script>

<style scoped>
.dataScreen {
  /* width: 100%; */
  height: 100vh;
  box-sizing: border-box;
  display: flex;
  flex-direction: row;
  align-items: center;
  overflow: hidden;
  transform-origin: 20% 20%;
  background-image: url("../assets/roverLandscape.jpg");
  background-size: cover;
  background-position-x: 80%;
  background-position-y: 5%;
  opacity: 0;
}
.dataScreenReady {
  animation: dataScreenFadeIn 1.5s cubic-bezier(0.21, 0.68, 0.25, 1) forwards;
}
.dataContainersReady {
  animation: dataContainerFadeIn 0.4s 1.5s ease-in-out forwards;
}
@keyframes dataScreenFadeIn {
  0% {
    opacity: 0;
    transform: scale(6);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
.tempDataContainer {
  width: 50%;
  height: 100vh;
  padding: 20px 40px 40px 40px;
  box-sizing: border-box;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.titleContainerLarge {
  height: 80px;
  flex: 0 0 80px;
  display: flex;
  flex-direction: column;
  align-items: center;
  visibility: hidden;
}
.titleContainerLarge .title {
  font-family: nasalization, sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 40px;
  letter-spacing: 5px;
  color: #b66222;
  animation: titleFadeIn 2s 2s ease-out reverse backwards;
}
.titleContainerLarge .subTitle {
  font-family: "Rubik", sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 20px;
  letter-spacing: 2px;
  color: #b66222;
  letter-spacing: 5px;
  animation: titleFadeIn 2s 2.5s ease-out reverse backwards;
}
.titleContainerSmall {
  height: 80px;
  flex: 0 0 80px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  display: none;
}
.titleContainerSmall .title {
  font-family: nasalization, sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 22px;
  letter-spacing: 5px;
  color: white;
  /* animation: titleFadeIn 2s 2s ease-out reverse backwards; */
}
.titleContainerSmall .subTitle {
  font-family: "Rubik", sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 16px;
  letter-spacing: 2px;
  color: white;
  letter-spacing: 5px;
  /* animation: titleFadeIn 2s 2.5s ease-out reverse backwards; */
}
.tempData {
  width: 100%;
  /* max-width: 600px; */
  height: calc(100% - 80px);
  /* max-height: 700px; */
  padding-top: 20px;
  flex: 0 0 calc(100% - 80px);
  background-color: rgba(58, 35, 21, 0.6);
  backdrop-filter: blur(4px);
  border-radius: 10px;
  visibility: hidden;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  visibility: hidden;
}
.no-temp-data {
  width: 100%;
  /* max-width: 600px; */
  height: calc(100% - 80px);
  /* max-height: 700px; */
  padding-top: 20px;
  flex: 0 0 calc(100% - 80px);
  background-color: rgba(58, 35, 21, 0.6);
  backdrop-filter: blur(4px);
  border-radius: 10px;
  visibility: hidden;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  visibility: hidden;
}
.row {
  width: 100%;
  padding: 15px 50px;
  box-sizing: border-box;
  font-family: "Rubik", sans-serif;
  color: white;
  font-weight: 300;
}
.label {
  color: rgb(209, 209, 209);
  font-size: 16px;
  margin-bottom: 7px;
}
.solTitle {
  font-weight: 400;
  font-size: 30px;
  margin-bottom: 4px;
}
.planetTitle {
  font-size: 22px;
  color: #f1b88b;
}
.earthDate {
  font-size: 40px;
  font-weight: 400;
  margin-bottom: 7px;
}
.tempRow {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.recentTempData {
  display: flex;
  flex-direction: column;
}
.recentWindData {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}
.recentTimestamp {
  /* align-self: center; */
  margin-top: 5px;
}
.data {
  font-size: 50px;
  font-weight: 400;
  font-family: "Rubik", sans-serif;
}
.colorBar {
  width: 100%;
  height: 12px;
  border-radius: 5px;
  background: rgb(80, 141, 255);
  background: linear-gradient(90deg, rgb(35, 102, 228) 0%, rgba(255, 255, 255, 1) 100%);
  border: 1px solid rgb(207, 207, 207);
  box-sizing: border-box;
  margin: 10px 0px;
}
.tempRange {
  display: flex;
  flex-direction: column;
}
.rangeTemps {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.highTemp {
  margin-left: 70px;
}
.nasaRow {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.noDataNasaRow {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-decoration: underline;
}
.nasaRow a {
  text-decoration: none;
  color: rgba(218, 218, 218, 0.856);
  transition: 0.2s all;
}
.nasaText {
  font-family: nasalization, sans-serif;
  font-weight: 300;
  font-style: normal;
  font-size: 14px;
}
.nasaRow a:hover {
  color: rgba(255, 255, 255, 0.856);
}
@keyframes dataContainerFadeIn {
  0% {
    opacity: 0;
    visibility: visible;
  }
  100% {
    opacity: 1;
    visibility: visible;
  }
}
@media (max-width: 1250px) {
  .temp {
    font-size: 35px;
  }
}
@media (max-width: 1100px) {
  .dataScreen {
    align-items: center;
    justify-content: center;
  }
  .tempDataContainer {
    width: 800px;
    padding: 20px;
  }
  .tempData {
    height: 100%;
    padding-top: 0px;
  }
  .no-temp-data {
    height: 100%;
    padding-top: 0px;
  }
  .titleContainerLarge {
    display: none;
  }
  .titleContainerSmall {
    display: flex;
  }
  .earthDate {
    font-size: 37px;
  }
}
@media (max-width: 500px) {
  .dataScreen {
    align-items: center;
    justify-content: center;
  }
  .tempDataContainer {
    height: 100%;
    padding: 0px;
    display: inline;
  }
  .tempData {
    height: 100%;
    max-height: 10000px;
    border-radius: 0px;
    backdrop-filter: blur(3px);
    background-color: rgba(39, 25, 16, 0.7);
    padding-top: 0px;
  }
  .no-temp-data {
    height: 100%;
    max-height: 10000px;
    border-radius: 0px;
    backdrop-filter: blur(3px);
    background-color: rgba(39, 25, 16, 0.7);
    padding-top: 0px;
  }
  .titleContainerLarge {
    display: none;
  }
  .earthDate {
    font-size: 35px;
  }
  .solTitle {
    font-weight: 400;
    font-size: 22px;
    margin-bottom: 2px;
  }
  .planetTitle {
    font-size: 18px;
  }
  .row {
    padding: 15px 40px;
  }
  .data {
    font-size: 40px;
  }
  .highTemp {
    margin-left: 40px;
  }
}
</style>
