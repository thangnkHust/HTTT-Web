<template>
  <div>
    <v-row class="mt-10" style="opacity: 0.85">
      <v-col cols="2" class="ml-10">
        <v-row no-gutters>
          <v-col cols="12">
            <v-card
              class="mx-auto"
              max-width="344"
              style="background-color: rgba(72 158 148 / 30%); border-color: rgba(97, 92, 92, 0.27);"
            >
              <v-row>
                <v-col
                  cols="12"
                  class="d-flex align-end justify-center"
                  style="padding-top: 13px"
                >
                  <v-icon size="3.75rem" color="white"
                    >mdi-weather-partly-cloudy</v-icon
                  >
                  <div class="text-h3 ml-3 font-weight-bold white--text">
                    {{ temp }}&deg;C
                  </div>
                </v-col>
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
              style="background-color: rgba(18 115 214 / 27%); border-color: rgba(97, 92, 92, 0.27);"
            >
              <v-card-text>
                <flip-countdown deadline="2022-1-1 00:00:00"></flip-countdown>
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
            <v-spacer></v-spacer>
            <v-menu bottom right>
              <template v-slot:activator="{ on, attrs }">
                <v-btn outlined color="grey darken-2" v-bind="attrs" v-on="on">
                  <span>{{ typeToLabel[type] }}</span>
                  <v-icon right>
                    mdi-menu-down
                  </v-icon>
                </v-btn>
              </template>
              <v-list>
                <v-list-item @click="type = 'month'">
                  <v-list-item-title>Month</v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>
          </v-toolbar>
        </v-sheet>
        <v-sheet height="600">
          <v-calendar
            ref="calendar"
            v-model="focus"
            color="primary"
            :type="type"
            @click:date="clickday($event)"
          ></v-calendar>
        </v-sheet>
      </v-col>
    </v-row>

    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="headline grey lighten-2 d-flex justify-center">
          Information
        </v-card-title>

        <v-card-text>
          {{ infomationDate }}
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions class="d-flex justify-center">
          <v-btn color="primary" text @click="dialog = false">
            Close
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
export default {
  name: "HelloWorld",
  components: { FlipCountdown },

  data: () => ({
    focus: "",
    infomationDate: {},
    type: "month",
    dialog: false,
    typeToLabel: {
      month: "Month",
    },
    selectedEvent: {},
    selectedElement: null,
    selectedOpen: false,
    events: [],
    weatherInterval: null,
    today: moment().format("dddd - DD/MM/YYYY"),
    now: moment().format("hh:mm:ss A"),
    infoWeather: {},
    temp: null,
  }),
  mounted() {
    this.$refs.calendar.checkChange();
    this.getInfoWeather();
    this.weatherInterval = setInterval(() => {
      this.getInfoWeather();
    }, 600000);
    setInterval(() => {
      this.now = moment().format("hh:mm:ss A");
      this.today = moment().format("dddd - DD/MM/YYYY");
    }, 1000);
  },
  methods: {
    getInfoWeather() {
      navigator.geolocation.getCurrentPosition(this.showPosition);
    },
    showPosition(position) {
      let location = position.coords.latitude + '+' + position.coords.longitude;
      console.log(location);
      axios
        .get(
          "https://wft-geo-db.p.rapidapi.com/v1/geo/locations/" + location +"/nearbyCities?radius=100",
          {
            headers:{
              'x-rapidapi-key': 'a7bd93d8damshc94cebeb5d84caap1521a9jsn2f6dd44d649a',
              'x-rapidapi-host': 'wft-geo-db.p.rapidapi.com'
            }
          }
        )
        .then((response) => {
          console.log(response.data.data[0].name);
          let city = response.data.data[0].name;
          axios
          .get(
            "https://api.openweathermap.org/data/2.5/weather?q=" + city  + "&appid=3265874a2c77ae4a04bb96236a642d2f"
          )
          .then((response) => {
            console.log(response.data);
            this.infoWeather = response.data;
            this.temp = Math.floor(response.data.main.temp - 273.15);
          })
          .catch((e) => {
            console.log(e);
          });
        });
    },
    clickday(event) {
      this.dialog = true;
      console.log(event, "Infomation Date");
      this.infomationDate = { ...event };
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
    showEvent({ nativeEvent, event }) {
      const open = () => {
        this.selectedEvent = event;
        this.selectedElement = nativeEvent.target;
        setTimeout(() => {
          this.selectedOpen = true;
        }, 10);
      };

      if (this.selectedOpen) {
        this.selectedOpen = false;
        setTimeout(open, 10);
      } else {
        open();
      }

      nativeEvent.stopPropagation();
    },
  },
};
</script>
