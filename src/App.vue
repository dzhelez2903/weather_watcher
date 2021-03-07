<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp>15 ? 'hot' : ''">
    <div class="main">
      <div class="search">
        <input
            class="search_input"
            type="text"
            placeholder="search..."
            v-model="query"
            @keypress="fetchWeather"
        />
      </div>
      <div class="weather" v-if="typeof weather.main != 'undefined'">
        <div class="location_container">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder () }}</div>
        </div>
        <div class="weather_container">
          <div class="temperature">
            {{ Math.round(weather.main.temp) }}&degC
          </div>
          <div class="weather">
            {{ weather.weather [0].main }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      api_key: '2cdac6125b9c12ea56115604b0ac04f8',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      language: 'ru',
      weather: {}
    }
  },
  methods: {

    fetchWeather (e) {
      if (e.key === "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}&lang={this.language}`)
            .then(res => {
              return res.json();
            }).then(this.setResults);

        setInterval(async ()=> {
          fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}&lang={this.language}`)
              .then(res => {
                return res.json();
              }).then(this.setResults);
        }, 300000);
      }
    },
    setResults (results) {
      this.weather = results;
      console.log(results)
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    }

  }
}
</script>

<style lang="scss">
@font-face {
  font-family: "Open Sans";
  src: url("./assets/fonts/Montserrat-Regular.ttf") format("ttf");
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Montserrat-Regular", sans-serif;
}

#app {
  background-image: url("./assets/img/cold_bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.5s;

  .main {
    min-height: 100vh;
    padding: 25px;
    background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));

    .search {
      width: 100%;
      margin-bottom: 30px;

      .search_input {
        display: block;
        width: 100%;
        padding: 15px;
        color: #313131;
        font-size: 20px;
        appearance: none;
        border: none;
        outline: none;
        background-color: rgba(255,255,255,0.3);
        border-radius: 0 16px 0 16px;
        box-shadow: 0 0 8px rgba(0,0,0,0.25);
        transition: 0.3s;

        &:focus {
          background-color: rgba(255,255,255,0.5);
          border-radius: 16px 0 16px 0;
          box-shadow: 0 0 16px rgba(0,0,0,0.25);
        }
      }
    }
    .location_container {
      color: #fff;
      text-align: center;

      .location {
        font-size: 32px;
        font-weight: 500;
        text-shadow: 1px 3px rgba(0,0,0,0.25);
      }
      .date {
        font-size: 20px;
        font-weight: 300;
        font-style: italic;
        text-shadow: 1px 3px rgba(0,0,0,0.25);
      }
    }
    .weather_container {
      text-align: center;
      color: #fff;

      .temperature {
        display: inline-block;
        padding: 10px 25px;
        font-size: 100px;
        font-weight: 900;
        text-shadow: 3px 6px rgba(0,0,0,0.25);
        background-color: rgba(255,255,255,0.15);
        border-radius: 16px;
        margin: 30px 0;
        box-shadow: 3px 6px rgba(0,0,0,0.25);
      }

      .weather {
        font-size: 48px;
        font-weight: 700;
        font-style: italic;
        text-shadow: 3px 6px rgba(0,0,0,0.25);
      }
    }
  }
}
#app.hot {
  background-image: url("./assets/img/warm_bg.jpg");
}
</style>
