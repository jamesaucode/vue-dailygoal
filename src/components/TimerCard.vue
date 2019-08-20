<template>
    <div class="timer-card">
        <input ref="input" v-model="name" @blur="stopEditing" @keydown.enter="stopEditing" v-if="editing" />
        <h1 @click="startEditing" v-else class="timer-card__heading">{{ name }}</h1>
        <span class="timer-card__time">{{ formattedTime }}</span>
        <button @click="startTimer">Start</button>
        <button class="stop" @click="stopTimer">Stop</button>
        <button @click="resetTimer">Reset</button>
    </div>
</template>

<script>
import { setInterval, clearInterval } from 'timers';
export default {
    name: "TimerCard",
    data() {
        return {
            name: "Timer",
            timeRemaining: 1500,
            timer: null,
            editing: false,
        }
    },
    computed: {
        formattedTime() {
            return new Date(this.timeRemaining * 1000).toISOString().substr(11, 8);
        }
    },
    methods: {
        startTimer() {
            this.timer = setInterval(() => {
                this.timeRemaining--;
            }, 1000)
        },
        stopTimer() {
            clearInterval(this.timer);
        },
        resetTimer() {
            this.timeRemaining = 1500;
        },
        startEditing() {
            this.editing = true;
        },
        stopEditing() {
            this.editing = false;
        }
    },
    watch: {
        editing: function(isEditing) {
            if (isEditing) {
                this.$nextTick(() => this.$refs.input.focus());
                this.$nextTick(() => this.$refs.input.select());
            }
        }
    }
}
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
        box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
        display: flex;
        flex-direction: column;
        margin: $large-gutter auto;
        max-width: 400px;
        padding: $medium-gutter;
        &__time {
            font-size: 1em;
            margin-bottom: $medium-gutter;
        }
        &__heading {
            font-size: 2em;
            font-weight: 400;
            margin-bottom: $medium-gutter;
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
            &.stop {
                border-color: #FF0000;
                color: #FF0000;
            }
        }
        input {
            width: 100%;
            font-size: 2em;
            text-align: center;
            margin-bottom: $medium-gutter;
        }
    }    
</style>