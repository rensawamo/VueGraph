<template>
  <div class="wrap">
    <div
      v-for="prefecture in prefectures"
      :key="prefecture.code"
      class="prefecture"
    >
      <label :for="prefecture.code">
        <input
          type="checkbox"
          :id="prefecture.code"
          :checked="prefecture.isCreateGraph"
          @click="
            changeGraph(
              prefecture.code,
              prefecture.name,
              prefecture.isCreateGraph
            )
          "
        />
        <span class="prefecture_name">{{ prefecture.name }}</span>
      </label>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      prefectures: [],
    };
  },
  mounted() {
    this.onAppear();
  },
  methods: {
    onAppear: function () {
      this.getPrefecturesInfo("prefectures")
        .then((response) => {
          this.prefectures = response.data.result.map((res) => {
            return {
              code: res["prefCode"],
              name: res["prefName"],
              isCreateGraph: false,
            };
          });
        })
        .catch((error) => {
          console.error(error.message);
        });
    },
    getPrefecturesInfo: function (path) {
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/${path}`,
        {
          headers: { "X-API-KEY": import.meta.env.VITE_MY_API_KEY },
        }
      );
      return response;
    },

    createGraph: function (code, name) {
      this.getPrefecturesInfo(
        `population/composition/perYear?cityCode=-&prefCode=${code}`
      )
        .then((response) => {
          var populations = [];
          var rawPopulations = response.data.result.data[0].data;
          for (let i = 0; i < rawPopulations.length; i++) {
            populations.push(rawPopulations[i]["value"]);
          }
          this.$emit("onAddSeries", code, name, populations);
          this.prefectures[code - 1].isCreateGraph = true;
        })
        .catch((error) => {
          console.error(error.message);
        });
    },
    deleteGraph: function (code) {
      this.$emit("onRemoveSeries", code);
      this.prefectures[code - 1].isCreateGraph = false;
    },
    changeGraph: function (code, name, isCreateGraph) {
      if (isCreateGraph) {
        this.deleteGraph(code);
      } else {
        this.createGraph(code, name);
      }
    },
  },
};
</script>
<style scoped>
.wrap {
  width: 500px;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
}
.prefecture_name {
  margin-left: 10px;
}
@media screen and (max-width: 425px) {
  .wrap {
    grid-template-columns: 1fr 1fr 1fr 1fr;

    margin-bottom: 100px;
  }
}
</style>
