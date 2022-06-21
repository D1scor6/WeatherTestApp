<template>
  <div id="app">
    <div class="container my-5" style="min-width: 360px;">
      <h1 class="title text-center">Тестовое задание</h1>

      <div class="card my-3 shadow-lg back-card overflow-hidden">
        <!-- Верхняя часть карточки -->
        <div class="card-top text-center" style="margin-bottom: 9rem">
          <div class="city-name my-3">
            <p>Ростов-на-Дону</p>
            <span>{{ new Date().toLocaleDateString("ru", { day: "2-digit"}) }}.{{ new Date().toLocaleDateString("ru", { month: "2-digit"}) }},
            
            {{ new Date().toLocaleDateString("ru", { weekday: "long"}) }}
            
            </span>
            <p>RU</p>
          </div>
        </div>
        <!-- Конец верхней части -->

        <div class="card-body">
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span><img v-bind:src='weather.weatherIcon'> {{ weather.temperature }}&deg;C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
              <div class="row"> 
                <div class="col-12 text-center temp">
                  <span>
                    {{ Math.round((weather.dayTemp + weather.eveTemp + weather.mornTemp + weather.nightTemp)/4) }}&deg;C
                  </span>
                  <p class="my-4">Средняя температура</p>
                </div>
              </div>
              <div class="row">
                <ul class="list-group list-group-horizontal d-flex justify-content-center">
                  <li class="list-group-item bg-transparent border-0 text-white" v-for="(day, index) in weather.forecast" :key="index" :day="day">
                  <span> {{ new Date(day.dt *1000).toLocaleString("ru-ru", {weekday: "long"}).toUpperCase() }} <br>

                     <img v-bind:src="`https://openweathermap.org/img/w/${day.weather[0].icon}.png`"><br>
                      Днем:{{ Math.round((day.temp.max)) }}<br>

                      Ночью:{{ Math.round((day.temp.min)) }}
                  </span>
                  </li>
                </ul>
              </div>
              
          </div>
        </div>

        <div class="card-bottom px-5 py-4 row">
          <div class="col text-center">
            <p>{{ weather.feelLike }}&deg;C</p>
            <span>Ощущается как</span>
          </div>
          <div class="col text-center">
            <p>{{ weather.humidity }}%</p>
            <span>Влажность</span>
          </div>
          
        </div>
          </div>
        </div>
        <div>
          <!-- <WeatherChart /> -->
        </div>
    </div>
  </div>
</template>

<script>
// import WeatherChart from './components/WeatherChart.vue';


export default {
    name: "App",
    // components: { 
    //   WeatherChart 
    // },
    data() {
        return {
            weather: {
                weatherIcon: "",
                temperature: 26,
                description: "Солнечно",
                dayTemp: "",
                eveTemp: "",
                mornTemp: "",
                nightTemp: "",
                feelLike: "",
                humidity: "",
                forecast: null
            },
            hist: {
                APIkey: "a6fe19a124c5d9995669f504964d8a83",
                lat: 47.2364,
                lon: 39.7139,
                fDay: new Date(),
                sDay: new Date(),
                tDay: new Date()
            }
        };
    },
    mounted: function () {
        this.getWeather();
        this.getHistForecast();
        this.dataUpdate();
    },
    methods: {
        getWeather: async function () {
            const key = "a6fe19a124c5d9995669f504964d8a83";
            const baseURL = `https://api.openweathermap.org/data/2.5/onecall?lat=47.2364&lon=39.7139&appid=${key}&units=metric&lang=ru`;
            const response = await fetch(baseURL);
            const data = await response.json();
            // console.log(data)
            this.weather.temperature = Math.round(data.hourly[0].temp);
            this.weather.description = data.daily[0].weather[0].description;
            this.weather.dayTemp = Math.round(data.daily[0].temp.day);
            this.weather.eveTemp = Math.round(data.daily[0].temp.eve);
            this.weather.mornTemp = Math.round(data.daily[0].temp.morn);
            this.weather.nightTemp = Math.round(data.daily[0].temp.night);
            this.weather.feelLike = Math.round(data.hourly[0].feels_like);
            this.weather.humidity = Math.round(data.daily[0].humidity);
            this.weather.weatherIcon = `https://openweathermap.org/img/w/${data.daily[0].weather[0].icon}.png`;
            this.weather.forecast = data.daily.slice(1, 4);
        },
        getHistForecast: async function () {
            this.hist.fDay.setUTCDate(this.hist.fDay.getUTCDate() - 1);
            this.hist.sDay.setUTCDate(this.hist.sDay.getUTCDate() - 2);
            this.hist.tDay.setUTCDate(this.hist.tDay.getUTCDate() - 3);
            const firstDay = `https://api.openweathermap.org/data/2.5/onecall/timemachine?lat=${this.hist.lat}&lon=${this.hist.lon}&dt=${Math.floor(this.hist.fDay.getTime() / 1000)}&appid=${this.hist.APIkey}&units=metric`;
            const response = await fetch(firstDay);
            const data = await response.json();
            console.log(data);
            const secondDay = `https://api.openweathermap.org/data/2.5/onecall/timemachine?lat=${this.hist.lat}&lon=${this.hist.lon}&dt=${Math.floor(this.hist.sDay.getTime() / 1000)}&appid=${this.hist.APIkey}&units=metric`;
            const response2 = await fetch(secondDay);
            const data2 = await response2.json();
            console.log(data2);
            const thirdDay = `https://api.openweathermap.org/data/2.5/onecall/timemachine?lat=${this.hist.lat}&lon=${this.hist.lon}&dt=${Math.floor(this.hist.tDay.getTime() / 1000)}&appid=${this.hist.APIkey}&units=metric`;
            const response3 = await fetch(thirdDay);
            const data3 = await response3.json();
            console.log(data3);
        },
        dataUpdate: function() {
          setInterval(this.getWeather, 60000 );
        }
    }
    
}
</script>

<style>
#app {
  position: absolute;
  height: 100%;
  width: 100%;
}

.title {
  font-size: 50px;
  font-weight: 500;
}

.back-card {
  border-radius: 50px !important;
  color: white;
  background: linear-gradient(to bottom right, #40b0dd, #043fad);
  text-shadow: 2px 2px 2px #707070;
}

.city-name {
  position: absolute;
  width: 100%;
}

.city-name p {
  font-weight: 400;
  font-size: 16pt;
}

.city-name span {
  position: relative;
  font-size: 18pt;
}

.temp span {
  font-weight: 100;
  font-size: 4em;
  letter-spacing: -5px;
  white-space: nowrap;
}
.card-mid {
  line-height: 0.5;
}

.card-mid .row {
  margin-top: 30px;
}

.condition {
  font-size: 1em;
  font-weight: 700;
  line-height: 1em;
  text-transform: capitalize;
}
.list-group {
  margin-left: 1em !important;
}
.list-group-item {
  text-shadow: 2px 2px 2px #707070;
  line-height: 1.5;
}

.high {
  font-size: 15px;
}

.low {
  font-size: 15px;
}

.card-bottom p {
  font-size: 50px;
  font-weight: 100;
  letter-spacing: -3px;
}
.card-bottom {
  margin-top: 1.8rem !important;
  line-height: 0.5;
}

.card-bottom span {
  font-size: 12px;
}

.form-control:focus {
  box-shadow: none;
  border-color: inherit;
}



/* #app {

} */
</style>
