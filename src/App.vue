<template>
  <main-screen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  />
  <interact-screen
    v-if="statusMatch === 'match'"
    :cardsInteract="settings.cardsContext"
    @onFinish="onGetResult"
  />
  <get-result
    v-if="statusMatch === 'result'"
    :timer="timer"
    @onStartAgain="statusMatch = 'default'"
  />
  <copy-right />
</template>

<script>
import MainScreen from "./components/MainScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import GetResult from "./components/GetResult.vue";
import CopyRight from "./components/CopyRight.vue";
import { shuffled } from "./utils/array";
export default {
  name: "App",
  components: {
    MainScreen,
    InteractScreen,
    GetResult,
    CopyRight,
  },
  data() {
    return {
      settings: {
        totalOfBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: 0,
    };
  },
  methods: {
    onGetResult() {
      //get timer
      this.timer = new Date().getDate() - this.settings.startedAt;
      //switch
      this.statusMatch = "result";
    },
    onHandleBeforeStart(config) {
      this.settings.totalOfBlocks = config.totalOfBlocks;

      const firstCards = Array.from(
        { length: this.settings.totalOfBlocks / 2 },
        (_, i) => i + 1
      );
      const secondCards = [...firstCards];
      const cards = [...firstCards, ...secondCards];
      this.settings.cardsContext = shuffled(
        shuffled(shuffled(shuffled(cards)))
      );
      this.settings.startedAt = new Date().getTime();

      //data ready
      this.statusMatch = "match";
    },
  },
};
</script>
