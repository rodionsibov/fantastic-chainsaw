<template>
  <div class="flex">
    <!-- Break -->
    <set-timer
      title="Break Length"
      :count="breakCount"
      :handleDecrease="handleBreakDecrease"
      :handleIncrease="handleBreakIncrease"
    />
    <!-- Session -->
    <set-timer
      title="Session Length"
      :count="sessionCount"
      :handleDecrease="handleSessionDecrease"
      :handleIncrease="handleSessionIncrease"
    />
  </div>

  <div class="clock-container">
    <h1>{{ currentTimer }}</h1>
    <span>{{ convertToTime(clockCount) }}</span>
    <audio
      ref="audio"
      preload="metadata"
      src="https://assets.mixkit.co/sfx/preview/mixkit-censorship-beep-1082.mp3"
    ></audio>

    <div class="flex">
      <button @click="handlePlayPause">
        <i class="fas" :class="[isPlaying ? 'fa-pause' : 'fa-play']"></i>
      </button>
      <button @click="handleReset"><i class="fas fa-sync"></i></button>
    </div>
  </div>
</template>

<script>
import SetTimer from "./components/SetTimer.vue";

export default {
  components: { SetTimer },
  name: "App",
  data() {
    return {
      breakCount: 5,
      sessionCount: 25,
      clockCount: 25 * 60,
      currentTimer: "Session",
      loop: null,
      isPlaying: false,
    };
  },
  watch: {
    sessionCount() {
      if (this.currentTimer === "Session") {
        this.clockCount = this.sessionCount * 60;
      }
    },
    breakCount() {
      if (this.currentTimer !== "Session") {
        this.clockCount = this.breakCount * 60;
      }
    },
  },
  methods: {
    convertToTime(count) {
      let minutes = Math.floor(count / 60);
      let seconds = count % 60;
      minutes = minutes < 10 ? "0" + minutes : minutes;
      seconds = seconds < 10 ? "0" + seconds : seconds;

      return `${minutes}:${seconds}`;
    },
    handleBreakDecrease() {
      if (this.breakCount > 1) {
        this.breakCount--;
      }
    },
    handleBreakIncrease() {
      if (this.breakCount < 60) {
        this.breakCount++;
      }
    },
    handleSessionDecrease() {
      if (this.sessionCount > 1) {
        this.sessionCount--;
      }
    },
    handleSessionIncrease() {
      if (this.sessionCount < 60) {
        this.sessionCount++;
      }
    },
    handlePlayPause() {
      if (this.isPlaying) {
        clearInterval(this.loop);
        this.isPlaying = false;
      } else {
        this.loop = setInterval(() => {
          if (this.clockCount === 0) {
            this.$refs.audio.play();
            this.currentTimer =
              this.currentTimer === "Session" ? "Break" : "Session";
            this.clockCount =
              this.currentTimer !== "Session"
                ? this.breakCount * 60
                : this.sessionCount * 60;
          } else {
            this.clockCount--;
            document.body.style.backgroundColor = `hsl(${this.clockCount}, 50%, 50%)`;
          }
        }, 1000);
        this.isPlaying = true;
      }
    },
    handleReset() {
      this.breakCount = 5;
      this.sessionCount = 25;
      this.clockCount = 25 * 60;
      this.currentTimer = "Session";
      this.isPlaying = false;
      clearInterval(this.loop);
      this.$refs.audio.pause();
      this.$refs.audio.currentTime = 0;
      document.body.style.backgroundColor = 'navy';
    },
  },
  unmounted() {
    clearInterval(this.loop);
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@100;400;700&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
}

body {
  background-color: navy;
  color: white;
  font-family: "Poppins", sans-serif;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  transition: 0.5s;
}

.flex {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
}

.time-container {
  margin: 0 20px;
}

.time-container span {
  font-weight: 700;
}

button {
  background-color: #111;
  cursor: pointer;
  border: none;
  padding: 15px;
  margin: 10px;
  color: white;
  font-size: 20px;
}

.actions-wrapper span {
  font-size: 40px;
  margin: 0 10px;
  width: 50px;
}

.clock-container {
  border: 2px solid white;
  border-radius: 10px;
  padding: 20px 40px;
  display: inline-block;
  margin-top: 40px;
  width: 250px;
}

.clock-container h1 {
  margin: 0;
  font-weight: 100;
}

.clock-container span {
  font-weight: 700;
  font-size: 60px;
}

h2 {
  font-weight: 100;
}
</style>
