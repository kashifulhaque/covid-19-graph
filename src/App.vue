<template>
  <div id="app" class="container">
    <br />
    <h1>COVID-19 😷 India graph. [Data from March 1, 2020]</h1>
    <p>
      <a :href="apiUrl" target="_blank" rel="noopener noreferrer"
        >JSON Data 🔗 (Complete data)</a
      >
    </p>
    <p>
      <a :href="api" target="_blank" rel="noopener noreferrer"
        >COVID-19 India 😷 API</a
      >
    </p>
    <span>
      <a :href="github" target="_blank" rel="noopener noreferrer"
        >Fork me on GitHub</a
      >
    </span>
    •
    <span>
      <a :href="bitbucket" target="_blank" rel="noopener noreferrer"
        >Fork me on BitBucket</a
      >
    </span>
    <div class="row mt-5" v-if="dailyConfirmed.length > 0">
      <div class="col">
        <h2>Daily confirmed</h2>
        <line-chart
          :chartData="dailyConfirmed"
          :options="chartOptions"
          label="Daily Confirmed"
          bgcolor="#303960"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="totalConfirmed.length > 0">
      <div class="col">
        <h2>Total confirmed</h2>
        <line-chart
          :chartData="totalConfirmed"
          :options="chartOptions"
          label="Total Confirmed"
          bgcolor="#303960"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="dailyRecovered.length > 0">
      <div class="col">
        <h2>Recoveries per day</h2>
        <line-chart
          :chartData="dailyRecovered"
          :options="chartOptions"
          label="# of people recovered everyday"
          bgcolor="#A8DF65"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="totalRecovered.length > 0">
      <div class="col">
        <h2>Total recoveries so far</h2>
        <line-chart
          :chartData="totalRecovered"
          :options="chartOptions"
          label="# of people recovered till now"
          bgcolor="#A8DF65"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="dailyDeceased.length > 0">
      <div class="col">
        <h2>Deaths per day due to COVID-19</h2>
        <line-chart
          :chartData="dailyDeceased"
          :options="chartOptions"
          label="# of people dying everyday"
          bgcolor="#D92027"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="totalDeceased.length > 0">
      <div class="col">
        <h2>Total # of deaths so far</h2>
        <line-chart
          :chartData="totalDeceased"
          :options="chartOptions"
          label="# of people dead till now"
          bgcolor="#D92027"
        ></line-chart>
      </div>
    </div>
    <br />
  </div>
</template>

<script>
import axios from "axios";
import LineChart from "./components/LineChart.vue";

export default {
  name: "App",
  components: {
    LineChart,
  },
  data() {
    return {
      tutorial: "https://youtu.be/cUSfL6MBmlY",
      api: "https://api.covid19india.org",
      apiUrl: "https://api.covid19india.org/data.json",
      github: "https://github.com/kashifulhaque/covid-19-graph",
      bitbucket: "https://bitbucket.org/kashifulhaque/covid-19-graph",
      dailyConfirmed: [],
      totalConfirmed: [],
      dailyRecovered: [],
      totalRecovered: [],
      dailyDeceased: [],
      totalDeceased: [],
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  async created() {
    const { data } = await axios.get(this.apiUrl);
    const historicData = data.cases_time_series;

    let i = 0;
    historicData.forEach((d) => {
      if (i > 30) {
        const {
          dailyconfirmed,
          totalconfirmed,
          dailyrecovered,
          totalrecovered,
          dailydeceased,
          totaldeceased,
          date,
        } = d;

        this.dailyConfirmed.push({ date, data: dailyconfirmed });
        this.totalConfirmed.push({ date, data: totalconfirmed });
        this.dailyRecovered.push({ date, data: dailyrecovered });
        this.totalRecovered.push({ date, data: totalrecovered });
        this.dailyDeceased.push({ date, data: dailydeceased });
        this.totalDeceased.push({ date, data: totaldeceased });
      }
      i++;
    });
  },
};
</script>
