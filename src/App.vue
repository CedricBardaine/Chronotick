<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center text-h6 text-sm-h3">Chronotick</div>

      <v-spacer></v-spacer>

      {{$vuetify.breakpoint.name}}

      <v-btn href="https://github.com/CedricBardaine" target="_blank" text>
        <span class="mr-2">CedricBardaine</span>
        <v-icon>mdi-github</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <!-- faire le button et graph là  -->
      <v-container grid-list-xs class="text-center">
        <v-btn class="ma-1" color="primary" @click="triggerChrono()">{{
          !started ? "start" : paused ? "resume" : "pause"
        }}</v-btn>
        <br />
        <!-- TODO: add vAlert to notify it needs a doubleclick  -->
        <v-btn
          class="ma-1"
          color="primary"
          :disabled="!started"
          @dblclick="reset()"
          >reset</v-btn
        >
        <v-row>

        <v-spacer v-show="!$vuetify.breakpoint.xs"></v-spacer>
        <v-row id="timer" >
          <v-col v-if="hours" justify="center">
            <v-row justify="center" class="text-h2 primary--text">
              {{ hours ? hours : "" }}
            </v-row>
            <v-row justify="center" class="text-h6 secondary--text">
              {{ hours ? "h" : "" }}
            </v-row>
          </v-col>
          <v-col>
            <v-row justify="center" class="text-h2 primary--text">
              {{ minutes }}
            </v-row>
            <v-row justify="center" class="text-h6 secondary--text"> m </v-row>
          </v-col>
          <v-col>
            <v-row justify="center" class="text-h2 primary--text">
              {{ seconds }}
            </v-row>
            <v-row justify="center" class="text-h6 secondary--text"> s </v-row>
          </v-col>
          <v-col>
            <v-row justify="center" class="text-h2 primary--text">
              {{ tenths }}
            </v-row>
            <v-row justify="center" class="text-h6 secondary--text"> ms </v-row>
          </v-col>
        </v-row>
        <v-spacer v-show="!$vuetify.breakpoint.xs"></v-spacer>
        </v-row>
        <v-btn color="primary" @click="addTick()" :disabled="!started"
          >tick</v-btn
        >
      </v-container>
    </v-main>

    actionBtnTxt : {{ actionBtnTxt }} <br />
    ticks : {{ ticks }} <br />
    displayCurrentTime : {{ displayCurrentTime }} <br />
    theTimer : {{ theTimer }} <br />
  </v-app>
</template>

<script>
import Timer from "easytimer.js"; // https://github.com/albert-gonzalez/easytimer.js

export default {
  name: "App",
  components: {},
  data: () => ({
    actionBtnTxt: ["start", "tick"],
    ticks: [], // in seconds
    theTimer: null,
    paused: false,
    started: false,

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
    totalSecs() {
      let nbSecs = (this.hours * 60 + this.minutes) * 60 + this.seconds;
      if (this.tenths >= 5) nbSecs++;
      return nbSecs;
    }
  },
  methods: {
    start() {
      this.theTimer.start({ precision: "secondTenths" });
    },
    triggerChrono() {
      if (this.hours + this.minutes + this.seconds + this.tenths <= 0) {
        this.theTimer.start({ precision: "secondTenths" });
        this.started = true;
      } else if (this.theTimer.isRunning()) {
        this.theTimer.pause();
        this.paused = true;
      } else {
        this.theTimer.start();
        this.paused = false;
      }
    },
    reset() {
      this.theTimer = new Timer();
      this.resetTimes();
      this.started = false;
      this.paused = false;
    },
    addTick() {
      this.ticks.push(this.totalSecs);
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
    resetTimes() {
      this.hours = 0;
      this.minutes = 0;
      this.seconds = 0;
      this.tenths = 0;
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
