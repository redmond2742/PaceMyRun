<template>
  <div class="app">
    <h1>Running Pace Calculator</h1>
    <p>Enter your goal finish time and distance to calculate your pace:</p>
    <form @submit.prevent>
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
    <div class="actions">
      <button
        class="button"
        type="button"
        :disabled="time <= 0"
        @click="logRun"
      >
        Log this result
      </button>
      <button
        class="button secondary"
        type="button"
        :disabled="loggedRuns.length === 0"
        @click="clearLog"
      >
        Clear log
      </button>
    </div>
    <section class="log">
      <div class="log-header">
        <h2>Comparison Log</h2>
        <p>Save multiple goal paces to compare side-by-side.</p>
      </div>
      <div v-if="loggedRuns.length === 0" class="empty-state">
        No entries yet. Tap “Log this result” to start comparing runs.
      </div>
      <table v-else>
        <thead>
          <tr>
            <th>Distance</th>
            <th>Finish Time</th>
            <th>Pace</th>
            <th>Logged</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(run, index) in loggedRuns" :key="run.id">
            <td>{{ run.distance }}</td>
            <td>{{ run.time }}</td>
            <td>{{ run.pace }}</td>
            <td>{{ run.timestamp }}</td>
            <td>
              <button
                class="link-button"
                type="button"
                @click="removeLog(index)"
              >
                Remove
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </section>
    <section class="share">
      <div class="share-header">
        <h2>Share your pace</h2>
        <p>
          Turn your goal into a shareable card for friends, training partners,
          and social media.
        </p>
      </div>
      <div class="share-card">
        <div class="share-title">Pace My Run</div>
        <div class="share-distance">{{ distance }}</div>
        <div class="share-time">{{ formattedTime }} finish</div>
        <div class="share-pace">{{ displayPace }}</div>
        <div class="share-tagline">#RunGoals #PaceMyRun</div>
      </div>
      <div class="share-actions">
        <button class="button" type="button" @click="copyShareText">
          Copy share text
        </button>
        <button class="button secondary" type="button" @click="shareResult">
          Share
        </button>
        <button class="button secondary" type="button" @click="downloadShareCard">
          Download share card
        </button>
      </div>
    </section>
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
      time: 3000,
      minTime: 0,
      maxTimeInSeconds: 28800, // 24 hours in seconds
      loggedRuns: [],
    };
  },

  methods: {
    padZero(value) {
      return (value < 10 ? "0" : "") + value;
    },
    decrementTime() {
      this.time = Math.max(this.time - 1, this.minTime);

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
      this.time = Number(t);
    },
    logRun() {
      if (this.time <= 0) {
        return;
      }

      this.loggedRuns.unshift({
        id: Date.now(),
        distance: this.distance,
        time: this.formattedTime,
        pace: this.displayPace,
        timestamp: new Date().toLocaleString(),
      });
    },
    removeLog(index) {
      this.loggedRuns.splice(index, 1);
    },
    clearLog() {
      this.loggedRuns = [];
    },
    async copyShareText() {
      const shareText = this.shareText;

      if (navigator.clipboard?.writeText) {
        await navigator.clipboard.writeText(shareText);
        return;
      }

      const textarea = document.createElement("textarea");
      textarea.value = shareText;
      textarea.style.position = "fixed";
      textarea.style.opacity = "0";
      document.body.appendChild(textarea);
      textarea.select();
      document.execCommand("copy");
      document.body.removeChild(textarea);
    },
    async shareResult() {
      const shareText = this.shareText;

      if (navigator.share) {
        await navigator.share({
          title: "Pace My Run",
          text: shareText,
        });
      } else {
        await this.copyShareText();
      }
    },
    downloadShareCard() {
      const canvas = document.createElement("canvas");
      const width = 1080;
      const height = 1350;
      canvas.width = width;
      canvas.height = height;
      const context = canvas.getContext("2d");

      if (!context) {
        return;
      }

      context.fillStyle = "#0f172a";
      context.fillRect(0, 0, width, height);

      context.fillStyle = "#38bdf8";
      context.fillRect(0, 0, width, 16);

      context.fillStyle = "#f8fafc";
      context.font = "bold 72px Arial";
      context.textAlign = "center";
      context.fillText("Pace My Run", width / 2, 220);

      context.font = "bold 110px Arial";
      context.fillText(this.distance, width / 2, 420);

      context.font = "normal 58px Arial";
      context.fillText(`${this.formattedTime} finish`, width / 2, 540);

      context.font = "bold 64px Arial";
      context.fillText(this.displayPace, width / 2, 660);

      context.font = "normal 46px Arial";
      context.fillStyle = "#94a3b8";
      context.fillText("#RunGoals #PaceMyRun", width / 2, 760);

      context.fillStyle = "#f8fafc";
      context.font = "normal 40px Arial";
      context.fillText(
        "Share your goal pace and tag your training crew.",
        width / 2,
        980
      );

      const link = document.createElement("a");
      link.href = canvas.toDataURL("image/png");
      link.download = "pace-my-run.png";
      link.click();
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
    displayPace() {
      return this.pace(this.distance);
    },
    shareText() {
      return `Goal: ${this.distance} in ${this.formattedTime} (${this.displayPace}). Ready to chase it! #RunGoals #PaceMyRun`;
    },
  },
};
</script>

<style>
.app {
  max-width: 620px;
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

.actions {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
  margin-top: 10px;
}

.log,
.share {
  margin-top: 30px;
  text-align: left;
}

.log-header,
.share-header {
  margin-bottom: 12px;
}

.log h2,
.share h2 {
  margin-bottom: 6px;
  font-size: 22px;
}

.empty-state {
  padding: 16px;
  background: #eef2f7;
  border-radius: 8px;
  font-size: 16px;
  color: #475569;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

th,
td {
  padding: 10px 8px;
  border-bottom: 1px solid #e2e8f0;
}

th {
  text-align: left;
  font-weight: 600;
  color: #0f172a;
}

.link-button {
  background: none;
  border: none;
  padding: 0;
  color: #2563eb;
  cursor: pointer;
  font-size: 14px;
}

.share-card {
  background: linear-gradient(135deg, #0f172a, #1e293b);
  color: #f8fafc;
  border-radius: 16px;
  padding: 20px;
  margin-bottom: 16px;
  text-align: center;
  box-shadow: 0 12px 24px rgba(15, 23, 42, 0.2);
}

.share-title {
  font-size: 18px;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: #38bdf8;
  margin-bottom: 10px;
}

.share-distance {
  font-size: 36px;
  font-weight: 700;
}

.share-time {
  font-size: 20px;
  margin-top: 6px;
}

.share-pace {
  font-size: 24px;
  margin-top: 10px;
}

.share-tagline {
  font-size: 14px;
  color: #94a3b8;
  margin-top: 14px;
}

.share-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  justify-content: center;
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

.button.secondary {
  background-color: #2563eb;
}

.button.secondary:hover {
  background-color: #1d4ed8;
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

  .actions,
  .share-actions {
    flex-direction: column;
  }

  table {
    font-size: 12px;
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
