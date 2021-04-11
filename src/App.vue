<template>
  <div id="app" class="cont">
    <br />
    <h1>COVID-19 India graph</h1>
    <h2>
      Vaccinated:
      {{
        this.vaccinated == 0
          ? "Loading ..."
          : Number(this.vaccinated.toFixed(1)).toLocaleString()
      }}
    </h2>
    <p>
      <a :href="apiUrl" target="_blank" rel="noopener noreferrer preconnect"
        >JSON Data 🔗 (Complete data)</a
      >
    </p>
    <p>
      <a :href="api" target="_blank" rel="noopener noreferrer preconnect"
        >COVID-19 India API</a
      >
    </p>
    <span>
      <a :href="github" target="_blank" rel="noopener noreferrer preconnect"
        >Fork me on GitHub</a
      >
    </span>
    •
    <span>
      <a :href="bitbucket" target="_blank" rel="noopener noreferrer preconnect"
        >Fork me on BitBucket</a
      >
    </span>
    <div class="row mt-5" v-if="dailyConfirmed.length > 0">
      <div class="col">
        <h2>
          Cases per day ({{
            this.dailyConfirmed[this.dailyConfirmed.length - 1].data
          }}
          cases today)
        </h2>
        <line-chart
          :chartData="dailyConfirmed"
          :options="chartOptions"
          label="Confirmed cases today"
          bgcolor="#303960"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="totalConfirmed.length > 0">
      <div class="col">
        <h2>
          Total number of cases till now ({{
            this.totalConfirmed[this.totalConfirmed.length - 1].data
          }}
          cases in total)
        </h2>
        <line-chart
          :chartData="totalConfirmed"
          :options="chartOptions"
          label="Number of confirmed cases till now"
          bgcolor="#303960"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="dailyRecovered.length > 0">
      <div class="col">
        <h2>
          Recoveries per day ({{
            this.dailyRecovered[this.dailyRecovered.length - 1].data
          }}
          recovered today)
        </h2>
        <line-chart
          :chartData="dailyRecovered"
          :options="chartOptions"
          label="Number of people recovered"
          bgcolor="#A8DF65"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="totalRecovered.length > 0">
      <div class="col">
        <h2>
          Total recoveries so far ({{
            this.totalRecovered[this.totalRecovered.length - 1].data
          }}
          recovered so far)
        </h2>
        <line-chart
          :chartData="totalRecovered"
          :options="chartOptions"
          label="Number of people recovered till now"
          bgcolor="#A8DF65"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="dailyDeceased.length > 0">
      <div class="col">
        <h2>
          Deaths per day ({{
            this.dailyDeceased[this.dailyDeceased.length - 1].data
          }}
          deaths today)
        </h2>
        <line-chart
          :chartData="dailyDeceased"
          :options="chartOptions"
          label="Number of deaths per day"
          bgcolor="#D92027"
        ></line-chart>
      </div>
    </div>
    <br />
    <div class="row mt-5" v-if="totalDeceased.length > 0">
      <div class="col">
        <h2>
          Total number of deaths so far ({{
            this.totalDeceased[this.totalDeceased.length - 1].data
          }}
          deaths so far)
        </h2>
        <line-chart
          :chartData="totalDeceased"
          :options="chartOptions"
          label="Number of deaths so far"
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
      apiV4: "https://api.covid19india.org/v4/data.json",
      github: "https://github.com/kashifulhaque/covid-19-graph",
      bitbucket: "https://bitbucket.org/kashifulhaque/covid-19-graph",
      vaccinated: 0,
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
    const dataV4 = await axios.get(this.apiV4);
    this.vaccinated = dataV4.data.TT.total.vaccinated;
    const historicData = data.cases_time_series;

    let i = 0;
    const skipDays = 91;
    historicData.forEach((d) => {
      if (i > skipDays) {
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

<style>
.cont {
  overflow: auto;
  padding: 0 4%;
}
</style>
