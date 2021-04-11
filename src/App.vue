<template>
  <div id="app" class="cont">
    <br />
    <h1 class="text-center">COVID-19 India graph</h1>
    <p class="text-center">Graph starts from May 1st, 2020</p>
    <h4 class="text-center">
      {{
        this.vaccinated == 0
          ? "Loading ..."
          : Number(this.vaccinated.toFixed(1)).toLocaleString() +
            " doses of vaccine currently administered 💉"
      }}
    </h4>
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
    <p class="text-center">
      Made by
      <a
        href="https://ifkash.me"
        target="_blank"
        rel="noreferrer noopener preconnect"
        >Kashiful Haque</a
      >
    </p>
    <div class="text-center">
      <a :href="github" target="_blank" rel="noopener noreferrer preconnect"
        >Fork me on GitHub
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-github"
          viewBox="0 0 16 16"
        >
          <path
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.012 8.012 0 0 0 16 8c0-4.42-3.58-8-8-8z"
          /></svg
      ></a>
    </div>
    <br />
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
  padding: 0 2%;
}
</style>
