<template>
  <div id="app">
    <h1 class="header">Daily Goal</h1>
    <div class="timer-wrapper">
      <timer-card
        v-for="(timer, index) in components"
        v-bind:key="timer"
        v-bind:index="index"
        v-bind:timerId="timer"
        v-bind:deleteTimer="deleteTimer"
      />
    </div>
    <Add @click.native="addTimer" />
  </div>
</template>

<script>
import uuid from "uuid/v4";
import "./style/reset.css";
import TimerCard from "./components/TimerCard.vue";
import Add from "./components/Add.vue";

export default {
  name: "app",
  data() {
    if (localStorage.getItem("Timers")) {
      return {
        components: JSON.parse(localStorage.getItem("Timers"))
      };
    }
    return {
      components: [uuid()]
    };
  },
  methods: {
    addTimer() {
      this.components.push(uuid());
      localStorage.setItem("Timers", JSON.stringify(this.components));
    },
    deleteTimer(timerToDelete) {
      this.components = this.components.filter(
        timer => timer !== timerToDelete
      );
      localStorage.setItem("Timers", JSON.stringify(this.components));
    }
  },
  components: {
    TimerCard,
    Add
  }
};
</script>

<style lang="scss">
#app {
  @import "./style/main.scss";
  box-sizing: border-box;
  font-family: $font-stack;
  font-size: calc(0.35vw + 16px);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: $medium-gutter;
  .header {
    font-size: 2.5em;
    font-weight: 500;
  }
}

.timer-wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);

  @media (max-width: 768px) {
    grid-template-columns: repeat(2, 1fr);
  }
  @media (max-width: 450px) {
    grid-template-columns: 1fr;
  }
}

svg {
  cursor: pointer;
  padding: $small-gutter;
}
</style>
