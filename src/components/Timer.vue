<template>
  <div :style="{ color: textColor }">Timer: {{ timer }}</div>
</template>

<script>
import eventHub from './events'

export default {
  data () {
    return {
      timer: 0,
      interval: null
    }
  },

  computed: {
    textColor () {
      return this.timer <= 10 ? 'red' : 'inherit';
    }
  },

  methods: {
    startTimer () {
      if (this.interval) return;

      this.timer--
      this.interval = setInterval(() => {
        if (this.timer <= 0) {
          this.stopTimer();
          eventHub.$emit('timer-zero');
        }

        this.timer--
      }, 1000);
    },

    stopTimer () {
      window.clearInterval(this.interval);
      this.interval = undefined;
    }
  },

  mounted () {
    eventHub.$on('start-timer', () => {
      this.startTimer()
    });

    eventHub.$on('set-timer', (value) => {
      this.stopTimer();
      this.timer = value;
    })
  },

  beforeDestroy () {
    eventHub.$off('start-timer');
    eventHub.$off('set-timer');
  }
}
</script>
