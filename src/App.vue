<template>
  <div id="app">
    <h1 class="header">Daily Goal</h1>
    <timer-card v-for="timer in components" v-bind:key="timer" />
    <Add @click.native="addTimer" />
  </div>
</template>

<script>
import "./style/reset.css";
import TimerCard from "./components/TimerCard.vue";
import Add from "./components/Add.vue";

export default {
  name: 'app',
  data() {
    return {
      components: ["timer"],
    }
  },
  methods: {
    addTimer() {
      this.components.push("timer");
    }
  },
  components: {
    TimerCard,
    Add,
  }
}
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

svg {
  cursor:pointer;
  padding: $small-gutter;
}
</style>
