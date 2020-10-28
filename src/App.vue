<template>
  <v-app>
    <!-- TODO: details the labels (the x axis) -->
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center text-h6 text-sm-h3">Chronotick</div>

      <v-spacer></v-spacer>

      {{ $vuetify.breakpoint.name }}

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
          @click="showError(5)"
          >reset</v-btn
        >
        <v-row>
          <v-spacer v-show="!$vuetify.breakpoint.xs"></v-spacer>
          <v-row id="timer">
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
              <v-row justify="center" class="text-h6 secondary--text">
                m
              </v-row>
            </v-col>
            <v-col>
              <v-row justify="center" class="text-h2 primary--text">
                {{ seconds }}
              </v-row>
              <v-row justify="center" class="text-h6 secondary--text">
                s
              </v-row>
            </v-col>
            <v-col>
              <v-row justify="center" class="text-h2 primary--text">
                {{ tenths }}
              </v-row>
              <v-row justify="center" class="text-h6 secondary--text">
                ms
              </v-row>
            </v-col>
          </v-row>
          <v-spacer v-show="!$vuetify.breakpoint.xs"></v-spacer>
        </v-row>
        <v-btn color="primary" @click="addCurrentTick()" :disabled="!started"
          >tick</v-btn
        >
        <v-row id="chart">
          <v-container fluid>
            <v-sparkline
              :fill="true"
              smooth="4"
              :labels="ticksBySecLabel"
              :value="ticksBySec"
              :gradient="['#669C5E', '#E8A90C']"
            ></v-sparkline>
          </v-container>
        </v-row>

        <div
          id="alertWrapper"
          style=""
          class="c-appearingVAlert"
          :class="showAlert ? 'c-show' : 'c-hide'"
        >
          <v-alert border="left" color="secondary" dark>
            Double click if you are sure to reset.
          </v-alert>
        </div>
      </v-container>
    </v-main>

    actionBtnTxt : {{ actionBtnTxt }} <br />
    ticks : {{ ticks }} <br />
    displayCurrentTime : {{ displayCurrentTime }} <br />
    theTimer : {{ theTimer }} <br />
    showAlert : {{ showAlert }} <br />
  </v-app>
</template>

<script>
import Timer from "easytimer.js"; // https://github.com/albert-gonzalez/easytimer.js

export default {
  name: "App",
  components: {},
  data: () => ({
    actionBtnTxt: ["start", "tick"],
    ticks: [],
    theTimer: null,
    paused: false,
    started: false,

    hours: 0,
    minutes: 0,
    seconds: 0,
    tenths: 0,

    formerSecond: 0,
    currentTicks: 0, // nb tick in this second
    ticksBySec: [],
    ticksBySecLabel: [],

    showAlert: false,

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
    },
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
    addCurrentTick() {
      this.currentTicks++;
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

      // if se second change we add a tick in the Array, because the chart can only render an Array of int.
      if (this.theTimer.getTotalTimeValues().seconds >= this.formerSecond) {
        this.ticksBySec.push(this.currentTicks);
        this.ticksBySecLabel.push(this.currentTicks);
        this.currentTicks = 0;

        this.formerSecond += 5;
      }
    },
    resetTimes() {
      this.hours = 0;
      this.minutes = 0;
      this.seconds = 0;
      this.tenths = 0;
    },
    showError(seconds) {
      let self = this;

      self.showAlert = true;
      setTimeout(() => {
        self.showAlert = false;
      }, seconds*1000);
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

<style >
.c-appearingVAlert {
  position: absolute;
  bottom: 0px;
  left: 0px;
  overflow: hidden;
  padding-right: 8px;
}
.c-hide{
    left: -500px;
  transition-duration: 1s;
  transition-timing-function: cubic-bezier(0.19, 1, 0.22, 1);
}
.c-show{
    left: 0px;
  transition-duration: 1s;
    transition-timing-function: cubic-bezier(0.19, 1, 0.22, 1);

}
</style>