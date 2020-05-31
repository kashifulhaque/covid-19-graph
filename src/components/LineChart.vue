<script>
import { Line } from "vue-chartjs";

export default {
  extends: Line,
  props: {
    label: {
      type: String,
    },
    bgcolor: {
      type: String,
    },
    chartData: {
      type: Array,
    },
    options: {
      type: Object,
    },
  },
  mounted() {
    const dates = this.chartData.map((d) => d.date);
    const data = this.chartData.map((d) => d.data);

    this.renderChart(
      {
        labels: dates,
        datasets: [
          {
            label: this.label,
            backgroundColor: this.bgcolor,
            data: data,
          },
        ],
      },
      this.options
    );
  },
};
</script>
