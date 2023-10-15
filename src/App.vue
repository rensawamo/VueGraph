<template>
  <div id="app">
    <div class="wrap">
      <div>
        <p class="check_title">都道府県</p>
        <CheckPrefecture
          @onAddSeries="addSeries"
          @onRemoveSeries="removeSeries"
        />
      </div>
      <div>
        <Highcharts class="highchart" :options="chartOptions" />
      </div>
    </div>
  </div>
</template>

<script>
import { Chart } from "highcharts-vue";
import CheckPrefecture from "./components/CheckPrefecture.vue";

export default {
  components: {
    Highcharts: Chart,
    CheckPrefecture: CheckPrefecture,
  },
  data() {
    return {
      chartOptions: {
        title: {
          text: "都道府県別の総人口推移グラフ",
        },
        credits: {
          enabled: false,
        },
        xAxis: {
          lineWidth: 1,
          tickWidth: 1,
          title: {
            align: "high",
            x: 30,
            offset: 15,
            text: "年度",
          },
          categories: [
            1960, 1965, 1970, 1975, 1980, 1985, 1990, 1995, 2000, 2005, 2010,
            2015, 2020, 2025, 2030, 2035, 2040, 2045,
          ],
        },
        yAxis: {
          lineWidth: 1,
          tickWidth: 1,
          title: {
            align: "high",
            offset: 10,
            text: "人口数",
            rotation: 0,
            y: -20,
          },
        },
        legend: {
          align: "right",
          verticalAlign: "top",
          layout: "vertical",
        },
        series: [],
      },
    };
  },
  methods: {
    addSeries: function (id, name, populations) {
      this.chartOptions.series.push({
        id: id,
        name: name,
        data: populations,
      });
    },
    removeSeries: function (id) {
      let index = this.chartOptions.series.findIndex(
        (series) => series.id === id
      );
      if (index !== -1) {
        this.chartOptions.series.splice(index, 1);
      } else {
        console.log("error delete");
      }
    },
  },
};
</script>

<style scoped></style>
