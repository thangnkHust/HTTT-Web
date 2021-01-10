<template>
  <div>
    <v-row class="mt-10" style="opacity: 0.85">
      <v-col cols="2" class="ml-10">
        <v-row no-gutters>
          <v-col cols="12">
            <v-card
              class="mx-auto"
              max-width="344"
              style="background-color: rgba(72 158 148 / 40%); border-color: rgba(97, 92, 92, 0.27);"
            >
              <v-row>
                <v-col
                  cols="12"
                  class="d-flex align-end justify-center"
                  style="padding-top: 13px"
                >
                  <v-icon size="3.75rem" color="white">
                    <!-- mdi-weather-cloudy -->
                    mdi-weather-partly-cloudy
                    <!-- {{ weather_icon[typeof infoWeather.weather === 'undefined' ? '' : infoWeather.weather[0].id] }} -->
                  <!-- <img :src="link" alt="icon"> -->
                  </v-icon>
                  <div class="text-h3 ml-3 font-weight-bold white--text">
                    {{ temp }}&deg;C
                  </div>
                </v-col>
                <v-col
                  cols="12"
                  align="center"
                  class="pt-0 text-subtitle-1 white--text"
                  > 
                  <v-icon size="1.5rem" color="white">
                    mdi-map-marker
                  </v-icon>
                    <span style="font-size: 1.2rem">{{infoWeather.name}}</span>
                  </v-col
                >
                <v-col
                  cols="12"
                  align="center"
                  class="pt-0 text-subtitle-1 white--text"
                  >{{ today }}</v-col
                >
                <v-col
                  cols="12"
                  align="center"
                  class="pt-0 text-h4 font-weight-bold white--text"
                  >{{ now }}</v-col
                >
              </v-row>
            </v-card>
          </v-col>
          <v-col cols="12" class="mt-4">
            <v-card
              style="background-color: rgba(72 158 148 / 40%); border-color: rgba(97, 92, 92, 0.27);"
            >
              <v-card-text>
                <flip-countdown deadline="2022-1-1 00:00:00" ></flip-countdown>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
      <v-spacer></v-spacer>
      <v-col class="mr-10" cols="9">
        <v-sheet height="64">
          <v-toolbar flat>
            <v-btn
              outlined
              class="mr-4"
              color="grey darken-2"
              @click="setToday"
            >
              Today
            </v-btn>
            <v-btn fab text small color="grey darken-2" @click="prev">
              <v-icon small>
                mdi-chevron-left
              </v-icon>
            </v-btn>
            <v-btn fab text small color="grey darken-2" @click="next">
              <v-icon small>
                mdi-chevron-right
              </v-icon>
            </v-btn>
            <v-toolbar-title v-if="$refs.calendar">
              {{ $refs.calendar.title }}
            </v-toolbar-title>
          </v-toolbar>
        </v-sheet>
        <v-sheet height="600">
          <v-calendar
            ref="calendar"
            v-model="focus"
            color="rgba(72 158 148 / 40%)"
            :type="type"
            @click:date="clickday($event)"
          ></v-calendar>
        </v-sheet>
      </v-col>
    </v-row>

    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="headline grey lighten-2 d-flex justify-center">
          Ngày {{ date_click.day }} Tháng {{ date_click.month}} Năm {{ date_click.year }}
        </v-card-title>

        <v-card-text>
          <p></p>
          <p><strong>Âm lịch: </strong> Ngày {{ typeof infomationDate.lunarDate === 'undefined' ? '' :  infomationDate.lunarDate.day}} Tháng {{ typeof infomationDate.lunarDate === 'undefined' ? '' :  infomationDate.lunarDate.month}} Năm {{ typeof infomationDate.lunarDate === 'undefined' ? '' :  infomationDate.lunarDate.yearCanChi }}</p>
          <p><strong>Giờ hoàng đạo: </strong> {{ infomationDate.gioHoangDao }}</p>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions class="d-flex justify-center">
          <v-btn color="primary" text @click="dialog = false">
            Đóng
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import FlipCountdown from "vue2-flip-countdown";
import calendar from "../js/calendar"
export default {
  name: "Calendar",
  components: { FlipCountdown },
  data: () => ({
    focus: "",
    infomationDate: {},
    type: "month",
    dialog: false,
    today: moment().format("dddd - DD/MM/YYYY"),
    now: moment().format("hh:mm A"),
    infoWeather: {},
    temp: null,
    date_click : moment().format("dddd - DD/MM/YYYY"),
    weather_icon: {
      200 : '',
      201 : '',
      202 : '',
      230 : '',
      231 : '',
      232 : '',
      233 : '',
      300 : '',
      301 : '',
      302 : '',
      500 : '',
      501 : '',
      502 : '',
      511 : '',
      521 : '',
      522 : 'mdi-weather-heavy-rain',
      600 : 'mdi-weather-snowy',
      601 : 'mdi-weather-snowy',
      602 : 'mdi-weather-snowy-heavy',
      610 : 'mdi-weather-snowy-rainy',
      611 : 'mdi-weather-snowy-heavy',
      612 : 'mdi-weather-snowy-heavy',
      621 : 'mdi-weather-snowy',
      622 : 'mdi-weather-snowy-heavy',
      623 : 'mdi-weather-snowy',
      700 : 'mdi-weather-hazy',
      711 : 'mdi-weather-hazy',
      721 : 'mdi-weather-hazy',
      731 : 'mdi-weather-hazy',
      741 : 'mdi-weather-fog',
      751 : 'mdi-weather-hazy',
      801 : 'weather-partly-cloudy',
      802 : 'weather-partly-cloudy',
      803 : "weather-partly-cloudy",
      900 : "mdi-weather-heavy-rain",
      804 : "mdi-cloud-alert",
      800 : "weather-partly-cloudy",
    },
  }),
  mounted() {
    // this.$refs.calendar.checkChange();
    // calendar.test();
    // console.log(this.infomationDate);
    this.getInfoWeather();
    setInterval(() => {
      this.getInfoWeather();
    }, 600000);
    setInterval(() => {
      this.now = moment().format("hh:mm A");
      this.today = moment().format("ddd - DD/MM/YYYY");
    }, 1000);
  },
  methods: {
    getInfoWeather() {
      navigator.geolocation.getCurrentPosition(this.showPosition);
    },

    showPosition(position) {
      let location = position.coords.latitude + "+" + position.coords.longitude;
      console.log(location);
      axios
        .get(
          "https://wft-geo-db.p.rapidapi.com/v1/geo/locations/" +
            location +
            "/nearbyCities?radius=100",
          {
            headers: {
              "x-rapidapi-key":
                "a7bd93d8damshc94cebeb5d84caap1521a9jsn2f6dd44d649a",
              "x-rapidapi-host": "wft-geo-db.p.rapidapi.com",
            },
          }
        )
        .then((response) => {
          console.log(response.data.data[0].name);
          let city = response.data.data[0].name;
          axios
            .get(
              "https://api.openweathermap.org/data/2.5/weather?q=" +
                city +
                "&appid=3265874a2c77ae4a04bb96236a642d2f"
            )
            .then((response) => {
              // console.log(response.data);
              this.infoWeather = response.data;
              this.temp = Math.floor(response.data.main.temp - 273.15);
              // this.link = 'https://openweathermap.org/img/wn/' + this.infoWeather.weather[0].icon + '@2x.png';
              // console.log(this.link);
              console.log(this.infoWeather.weather[0].id);
            })
            .catch((e) => {
              alert("Get location error");
              axios
                .get(
                  "https://api.openweathermap.org/data/2.5/weather?q=hanoi&appid=3265874a2c77ae4a04bb96236a642d2f"
                )
                .then((response) => {
                  console.log(response.data);
                  this.infoWeather = response.data;
                  this.temp = Math.floor(response.data.main.temp - 273.15);
                  // this.link = 'https://openweathermap.org/img/wn/' + this.infoWeather.weather[0].icon + '@2x.png';
                  // console.log(this.link);
                  console.log(this.infoWeather.weather[0].id);
                });
              console.log(e);
            });
        });
    },
    clickday(event) {
      this.date_click = event;
      this.dialog = true;
      console.log(event);
      var jd = calendar.jdn(event.day, event.month, event.year);
      var gioHoangDao = calendar.getGioHoangDao(jd);
      var lunarDate = calendar.getLunarDate(event.day, event.month, event.year);
      lunarDate.yearCanChi = calendar.getYearCanChi(lunarDate.year);
      this.infomationDate.gioHoangDao = gioHoangDao;
      this.infomationDate.lunarDate = lunarDate;
      console.log(this.infomationDate);
    },

    setToday() {
      this.focus = "";
    },
    prev() {
      this.$refs.calendar.prev();
    },
    next() {
      this.$refs.calendar.next();
    },
  },
};
</script>

<style>
  .v-calendar-weekly__day.v-past.v-outside:first-child .v-btn__content,
  .v-calendar-weekly__day:first-child .v-btn__content,
  .v-calendar-weekly__head-weekday:first-child {
    color: red !important;
  }
  /* .v-outside > *{
    pointer-events: none;
    opacity: 0;
  } */

  .flip-clock__slot[data-v-78efe7f6] {
    color : white;
  }

  .flip-card__top[data-v-78efe7f6], .flip-card__bottom[data-v-78efe7f6], .flip-card__back-bottom[data-v-78efe7f6], .flip-card__back[data-v-78efe7f6]::before, .flip-card__back[data-v-78efe7f6]::after{
    color: #fff !important;
  }
</style>
