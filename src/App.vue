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
        <v-btn color="primary" @click="counting ? addTick() : start()">{{
          counting ? "tick" : "start"
        }}</v-btn>
        <v-row>
          <v-col>
            <v-btn color="primary">pause</v-btn>
          </v-col>
          <v-col>
            <v-btn color="primary">reset</v-btn>
          </v-col>
        </v-row>
        <v-btn text class="text-h1" color="secondary">{{
          theTimer_elapsed ? theTimer_elapsed : "0"
        }}</v-btn>
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
    now: 0,
    theTimer: null,
    theTimer_elapsed: "",
  }),
  computed: {
    displayCurrentTime() {
      // return Math.round((this.now - this.startTime) / 1000);
      return this.theTimer
        ? this.theTimer.getTimeValues().minutes +
            ":" +
            this.theTimer.getTimeValues().seconds
        : "";
    },
  },
  methods: {
    updateNow() {
      this.now = Date.now();
    },
    start() {
      // this.updateNow();
      // setInterval(this.updateNow.bind(this), 1000); // https://medium.com/vuejs-tips/i-want-it-now-ca6c89dded6c

      // this.counting = true;
      // this.startTime = Date.now();

      this.theTimer.start({precision: 'secondTenths'});
      console.log(this.theTimer);
    },
    addTick() {
      this.ticks.push(Date.now());
    },
    displayReadableTimer(aEasyTimer) {
      let hours = aEasyTimer.getTimeValues().hours;
      let minutes = aEasyTimer.getTimeValues().minutes;
      let secondes = aEasyTimer.getTimeValues().seconds;
      let tenths = aEasyTimer.getTimeValues().secondTenths;
      let ret = "";

      if (!secondes) ret = "0";
      else {
        ret += hours ? hours + ":" : '';
        ret += minutes ? minutes + ":" : '';
        ret += secondes ? secondes + ":" : '';
        ret += tenths;
      }

      return ret;
    },
  },
  mounted() {
    this.theTimer = new Timer();
    let self = this;

    this.theTimer.addEventListener("secondTenthsUpdated", function (e) {
      self.theTimer_elapsed = self.displayReadableTimer(self.theTimer);
      console.log(e);
    });
  },
};
</script>
