<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center text-h6 text-sm-h3">Chronotick</div>

      <v-spacer></v-spacer>

      <v-btn href="https://github.com/CedricBardaine" target="_blank" text>
        <span class="mr-2">CedricBardaine</span>
        <v-icon>mdi-github</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <!-- faire le button et graph là  -->
      <v-container grid-list-xs class="text-center">
        <v-btn color="primary" @click="triggerChrono">!!!</v-btn>
        <v-row>
          <v-col>
            <v-btn color="primary" @click="triggerChrono()">noting</v-btn>
          </v-col>
          <v-col>
            <v-btn color="primary">reset</v-btn>
          </v-col>
        </v-row>
        <div id="timer">
          <span class="text-h2 primary--text">
            {{ hours ? hours : "" }}
          </span>
          <span class="text-h6 secondary--text">
            {{ hours ? "h" : "" }}
          </span>
          <span class="text-h2 primary--text"> {{ minutes }} </span>
          <span class="text-h6 secondary--text"> m </span>
          <span class="text-h2 primary--text"> {{ seconds }} </span>
          <span class="text-h6 secondary--text"> s </span>
          <span class="text-h2 primary--text"> {{ tenths }} </span>
          <span class="text-h6 secondary--text"> ms </span>
        </div>
      </v-container>
    </v-main>

    actionBtnTxt : {{ actionBtnTxt }} <br />
    onPause : {{ onPause }} <br />
    counting : {{ counting }} <br />
    startTime : {{ startTime }} <br />
    ticks : {{ ticks }} <br />
    displayCurrentTime : {{ displayCurrentTime }} <br />
    theTimer : {{ theTimer }} <br />

    <!-- TODO: faire en sorte que le date.now puisse etre détecté et que le chrono marche , vérifier les start stop reset et faire que ca marche tout ca bref -->
  </v-app>
</template>

<script>
import Timer from "easytimer.js"; // https://github.com/albert-gonzalez/easytimer.js

export default {
  name: "App",
  components: {},
  data: () => ({
    actionBtnTxt: ["start", "tick"],
    onPause: false,
    counting: false,
    startTime: 0,
    ticks: [],
    theTimer: null,

    hours: 0,
    minutes: 0,
    seconds: 0,
    tenths: 0,

    theTimer_elapsed: "",
  }),
  computed: {
    displayCurrentTime() {
      return this.theTimer
        ? this.theTimer.getTimeValues().minutes +
            ":" +
            this.theTimer.getTimeValues().seconds
        : "";
    },
  },
  methods: {
    start() {
      console.log(this.theTimer.isPaused());
      this.theTimer.start({ precision: "secondTenths" });
      console.log(this.theTimer.isPaused());
    },
    triggerChrono() {
      if (this.hours + this.minutes + this.seconds + this.tenths <= 0) {
        this.theTimer.start({ precision: "secondTenths" });
      } else if (this.theTimer.isRunning()) {
        this.theTimer.pause();
      } else {
        this.theTimer.start();
      }
    },
    addTick() {
      this.ticks.push(Date.now());
    },
    /**
     * @deprecated
     */
    displayReadableTimer(aEasyTimer) {
      let hours = aEasyTimer.getTimeValues().hours;
      let minutes = aEasyTimer.getTimeValues().minutes;
      let secondes = aEasyTimer.getTimeValues().seconds;
      let tenths = aEasyTimer.getTimeValues().secondTenths;
      let ret = "";

      if (!this.theTimer) ret = "0";
      else {
        ret += hours ? hours + "h" : "";
        ret += minutes ? minutes + "m" : "";
        ret += secondes + "s";
        ret += tenths + "ms";
      }

      return ret;
    },
    updateTimes() {
      this.hours = this.theTimer.getTimeValues().hours;
      this.minutes = this.theTimer.getTimeValues().minutes;
      this.seconds = this.theTimer.getTimeValues().seconds;
      this.tenths = this.theTimer.getTimeValues().secondTenths;
    },
  },
  mounted() {
    let self = this;
    this.theTimer = new Timer();
    console.log(this.theTimer);

    this.theTimer.addEventListener("secondTenthsUpdated", function () {
      self.updateTimes();
    });
  },
};
</script>
