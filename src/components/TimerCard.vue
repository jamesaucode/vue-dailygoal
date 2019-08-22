<template>
  <div class="timer-card">
    <input
      ref="input"
      v-model="name"
      @blur="stopEditing"
      @keydown.enter="stopEditing"
      v-if="editing"
    />
    <h1 @click="startEditing" v-else class="timer-card__heading">{{ name }}</h1>
    <input
      ref="input"
      v-model.number="initTime"
      @blur="stopEditingTime"
      @keydown.enter="stopEditingTime"
      v-if="editingTime"
    />
    <span v-else @click="startEditingTime" class="timer-card__time">{{ formattedTime }}</span>
    <div class="timer-card__controls">
      <Stop v-if="playing" @click.native="stopTimer" />
      <Start v-else @click.native="startTimer" />
    </div>
    <div
      class="progress"
      v-bind:style="{
        transform: 'scaleX' + '(' + timeRemaining / initTime + ')'
      }"
    ></div>
    <div class="timer-card__controls"></div>
    <Delete class="delete" @click.native="handleDeleteTimer" />
  </div>
</template>

<script>
import { setInterval, clearInterval } from "timers";
import Delete from "./Delete.vue";
import Start from "./Start.vue";
import Stop from "./Stop.vue";

export default {
  name: "TimerCard",
  components: {
    Delete,
    Start,
    Stop
  },
  props: {
    timerId: String,
    deleteTimer: Function,
    index: Number
  },
  created() {
    window.addEventListener("beforeunload", this.save);
  },
  data() {
    if (localStorage.getItem(this.$props.timerId)) {
      return JSON.parse(localStorage.getItem(this.$props.timerId));
    }
    return {
      name: "New Timer",
      initTime: 100,
      timeRemaining: 100,
      timer: null,
      editing: false,
      editingTime: false,
      playing: false
    };
  },
  computed: {
    formattedTime() {
      return new Date(this.timeRemaining * 1000).toISOString().substr(11, 8);
    }
  },
  methods: {
    startTimer() {
      if (this.timeRemaining === 0) {
        return;
      }
      this.timer = setInterval(() => {
        this.timeRemaining--;
        if (this.timeRemaining <= 0) {
          this.stopTimer();
        }
      }, 1000);
      this.$nextTick(() => (this.playing = true));
    },
    stopTimer() {
      clearInterval(this.timer);
      this.$nextTick(() => (this.playing = false));
    },
    resetTimer() {
      this.timeRemaining = this.initTime;
    },
    startEditing() {
      this.editing = true;
    },
    stopEditing() {
      this.editing = false;
    },
    startEditingTime() {
      this.editingTime = true;
    },
    stopEditingTime() {
      this.editingTime = false;
      this.timeRemaining = this.initTime;
    },
    handleDeleteTimer() {
      localStorage.removeItem(this.$props.timerId);
      this.$props.deleteTimer(this.$props.timerId);
      window.removeEventListener("beforeunload", this.save);
    },
    save() {
      window.localStorage.setItem(
        this.$props.timerId,
        JSON.stringify(this.$data)
      );
    }
  },
  watch: {
    editing: function(isEditing) {
      if (isEditing) {
        this.$nextTick(() => this.$refs.input.focus());
        this.$nextTick(() => this.$refs.input.select());
      }
    },
    editingTime: function(isEditing) {
      if (isEditing) {
        this.$nextTick(() => this.$refs.input.focus());
        this.$nextTick(() => this.$refs.input.select());
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../style/main.scss";
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
.timer-card {
  align-items: center;
  animation: fadeIn 500ms 1 ease;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.15);
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  margin: $medium-gutter;
  padding: $medium-gutter;
  position: relative;
  &__controls {
    display: flex;
  }
  &__time {
    font-size: 1em;
    margin-bottom: $medium-gutter;
  }
  &__heading {
    font-size: 1.5em;
    font-weight: 400;
    margin-bottom: $medium-gutter;
  }
  .delete {
    cursor: pointer;
    padding: $small-gutter;
    fill: #ff0000;
    position: absolute;
    top: -20px;
    right: -20px;
  }
  button {
    border: 1px solid #00bf5d;
    border-radius: 3px;
    background-color: transparent;
    color: #00bf5d;
    cursor: pointer;
    font-family: $font-stack;
    font-size: 0.8em;
    padding: 8px 24px;
    &.red {
      border-color: #ff0000;
      color: #ff0000;
    }
  }
  input {
    width: 100%;
    font-size: 1.5em;
    text-align: center;
    margin-bottom: $medium-gutter;
  }
  .progress {
    width: 100%;
    height: 5px;
    position: absolute;
    bottom: 0;
    background-color: green;
    transition: 200ms ease transform;
    transform-origin: left;
  }
}
</style>
