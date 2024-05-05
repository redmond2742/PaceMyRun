<template>
  <div class="app">
    <h1>Running Pace Calculator</h1>
    <p>Enter your goal finish time and distance to calculate your pace:</p>
    <form>
      <label for="distance">Distance:</label>
      <select v-model="distance" id="distance">
        <option value="5K">5K (3.1 miles)</option>
        <option value="10K">10K (6.2 miles)</option>
        <option value="Half Marathon">Half Marathon (13.1 miles)</option>
        <option value="Marathon">Marathon (26.2 miles)</option>
        <option value="2 Miles">2 Miles</option>
        <option value="3 Miles">3 Miles</option>
        <option value="4 Miles">4 Miles</option>
        <option value="5 Miles">5 Miles</option>
        <option value="6 Miles">6 Miles</option>
        <option value="7 Miles">7 Miles</option>
        <option value="8 Miles">8 Miles</option>
        <option value="9 Miles">9 Miles</option>
        <option value="10 Miles">10 Miles</option>
        <option value="11 Miles">11 Miles</option>
        <option value="12 Miles">12 Miles</option>
        <option value="13 Miles">13 Miles</option>
        <option value="14 Miles">14 Miles</option>
        <option value="15 Miles">15 Miles</option>
        <option value="16 Miles">16 Miles</option>
        <option value="17 Miles">17 Miles</option>
        <option value="18 Miles">18 Miles</option>
        <option value="19 Miles">19 Miles</option>
        <option value="20 Miles">20 Miles</option>
      </select>
      <div class="slider-container">
        <label for="time">Goal Finish Time:</label><br />
        <div class="container">
          <button
            class="button"
            type="button"
            @click="decrementTime()"
            :disabled="time <= 0"
          >
            -
          </button>
          &nbsp;&nbsp;&nbsp;
          <output>{{ formattedTime }}</output>
          &nbsp;&nbsp;&nbsp;
          <button
            class="button"
            type="button"
            @click="incrementTime()"
            :disabled="time >= maxTimeInSeconds"
          >
            +</button
          ><br />
        </div>

        <input
          type="range"
          v-model="time"
          id="slider"
          :min="minTime"
          :max="maxTimeInSeconds"
          @input="updateSliderTime(time)"
        />

        <label for="pace">Pace (HH:MM:SS):</label><br />
        <output>{{ displayPace }}</output>
      </div>
    </form>
  </div>
  <footer>
    <p>
      View this project on
      <a href="https://github.com/redmond2742/PaceMyRun">GitHub</a>
    </p>
  </footer>
</template>

<script>
export default {
  data() {
    return {
      distance: "5K",
      time: 0,
      minTime: "0",
      maxTimeInSeconds: 28800, // 24 hours in seconds
      myData: "",
      displayPace: "TBD",
    };
  },

  methods: {
    padZero(value) {
      return (value < 10 ? "0" : "") + value;
    },
    decrementTime() {
      this.time = this.time - 1;

      this.pace(this.distance);
    },
    incrementTime() {
      this.time = parseInt(this.time) + parseInt(1);

      this.pace(this.distance);
    },

    pace(d) {
      const distanceInMiles = {
        "5K": 3.1,
        "10K": 6.2,
        "Half Marathon": 13.1,
        Marathon: 26.2,
        "2 Miles": 2,
        "3 Miles": 3,
        "4 Miles": 4,
        "5 Miles": 5,
        "6 Miles": 6,
        "7 Miles": 7,
        "8 Miles": 8,
        "9 Miles": 9,
        "10 Miles": 10,
        "11 Miles": 11,
        "12 Miles": 12,
        "13 Miles": 13,
        "14 Miles": 14,
        "15 Miles": 15,
        "16 Miles": 16,
        "17 Miles": 17,
        "18 Miles": 18,
        "19 Miles": 19,
        "20 Miles": 20,
      }[d];

      const paceInSeconds = this.time / distanceInMiles;

      const paceHours = Math.floor(paceInSeconds / 3600);
      const paceMinutes = Math.floor((paceInSeconds % 3600) / 60);
      const paceSeconds = Math.round(paceInSeconds % 60);

      return `${this.padZero(paceHours)}:${this.padZero(
        paceMinutes
      )}:${this.padZero(paceSeconds)} per mile`;
    },
    updateSliderTime(t) {
      this.time = t;
    },
  },
  computed: {
    formattedTime() {
      const hours = Math.floor(this.time / 3600);
      const minutes = Math.floor((this.time % 3600) / 60);
      const seconds = this.time % 60;

      return `${this.padZero(hours)}:${this.padZero(minutes)}:${this.padZero(
        seconds
      )}`;
    },
  },
  mounted() {
    this.time = 3000;
    this.displayPace = this.pace(this.distance);
  },
  updated() {
    this.displayPace = this.pace(this.distance);
  },
  watch: {
    myData() {
      this.displayPace = this.pace(this.distance);
    },
  },
};
</script>

<style>
.app {
  max-width: 400px;
  margin: 40px auto;
  padding: 20px;
  background-color: #f9f9f9;
  border: 1px solid #ccc;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
.container {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: 20px;
}

h1 {
  font-size: 36px;
  margin-bottom: 10px;
}

form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

label {
  font-size: 24px;
  margin-bottom: 10px;
}

input[type="text"],
select {
  font-size: 24px;
  padding: 10px;
  width: 100%;
  margin-bottom: 20px;
}

.slider-container {
  margin-top: 20px;
}

input[type="range"] {
  width: 100%;
}

output {
  font-size: 48px;
  font-weight: bold;
  margin-bottom: 20px;
}
.button-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 10px;
}

.button {
  background-color: #4caf50;
  color: #fff;
  border: none;
  padding: 10px 20px;
  font-size: 28px;
  cursor: pointer;
  border-radius: 5px;
  margin-bottom: 20px;
}

.button:hover {
  background-color: #3e8e41;
}

.button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

/* Make it mobile-friendly */
@media only screen and (max-width: 600px) {
  .app {
    width: 100%;
    margin: 0;
    padding: 10px;
  }
  h1 {
    font-size: 24px;
  }
  label {
    font-size: 18px;
  }
  input[type="text"],
  select {
    font-size: 18px;
  }
  .slider-container {
    margin-top: 10px;
  }
  output {
    font-size: 36px;
  }
}

footer {
  background-color: #f9f9f9;
  padding: 10px;
  text-align: center;
  font-size: 14px;
  color: #666;
}
</style>
