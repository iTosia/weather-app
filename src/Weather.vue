<template>
  <div
    id="app"
    v-bind:class="
      typeof weather.main != 'undefined' && weather.main.temp < 15 ? 'cold' : ''
    "
  >
    <main>
      <div class="search">
        <input
          class="search-bar"
          type="text"
          placeholder="Search..."
          v-model="query"
          v-on:keypress="fetchWeather"
        />
      </div>

      <!-- error response -->
      <div class="weather-wrap" v-if="errorRes">
        <h2 class="error-msg">Sorry, there was a problem...</h2>
      </div>
      <!-- /error response -->
      <div
        class="weather-wrap"
        :class="{ hide: active }"
        v-if="typeof weather.main != 'undefined'"
      >
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
          <div class="weather-pressure">
            <span class="pressure">Pressure:</span>
            {{ weather.main.pressure }} Pa
          </div>
          <div class="weather-humidity">
            <span class="humidity">Humidity:</span>
            {{ weather.main.humidity }} %
          </div>
          <div class="weather-wind">
            <span class="wind">Wind speed:</span> {{ weather.wind.speed }} km/h
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "Weather",
  data() {
    return {
      api_key: "bb0a59c951859589328b2a7ae1b009a1",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      errorRes: false,
      active: false,
    };
  },
  methods: {
    fetchWeather(e) {
      this.errorRes = false;
      this.active = true;
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then(function(res) {
            if (res.status !== 200) {
              return Promise.reject(new Error(res.statusText));
            }
            return Promise.resolve(res);
          })
          .then((res) => {
            return res.json();
          })
          .then(this.setResults)
          .catch((error) => {
            console.log(error);
            this.errorRes = true;
            this.active = true;
          });
        this.active = false;
      }
    },
    setResults(results) {
      this.weather = results;
    },
    dateBuilder() {
      let now = new Date();
      let options = {
        weekday: "long",
        year: "numeric",
        month: "long",
        day: "numeric",
      };
      return now.toLocaleDateString("en-US", options);
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: "montserrat", sans-serif;
}
#app {
  background-image: url("./assets/warm-bg.jpg");
  background-size: cover;
  background-position: top;
  transition: 0.4s;
}
#app.cold {
  background-image: url("./assets/cold-bg.jpg");
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.search {
  width: 100%;
  margin-bottom: 10%;
}
.search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  background-color: rgba(255, 255, 255, 0.5);
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  border-radius: 10px;
  transition: 0.4s;
}
.search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
}
.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #fff;
  font-size: 36px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-pressure,
.weather-humidity,
.weather-wind {
  color: #fff;
  font-size: 24px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  margin-top: 10px;
}
.pressure,
.humidity,
.wind {
  font-style: italic;
}
.error-msg {
  font-family: "Montserrat", sans-serif;
  color: #fff;
  text-align: center;
  font-size: 36px;
  line-height: 46px;
  background-color: red;
  border-radius: 20px;
  padding: 30px 10px;
}
.hide {
  display: none;
}
@media all and (max-width: 420px) {
  .error-msg {
    font-size: 16px;
    line-height: 21px;
  }
}
</style>
