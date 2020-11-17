<template>
  <div class="home">
    <h2>A page with Three dropdown and 1 scatter plot chart</h2>
    <select v-model="sector_selected">
      <option
        v-for="sector in sectors"
        v-bind:value="sector.SectorNumber"
        v-bind:key="sector.SectorNumber"
      >
        {{ sector.SectorNameEN }}
      </option>
    </select>
    <select v-model="AFeature_selected">
      <option
        v-for="feature in features"
        v-bind:value="{ id: feature.AccountCode, text: feature.AccountNameEN }"
        v-bind:key="feature.AccountCode"
      >
        {{ feature.AccountNameEN }}
      </option>
    </select>
    <select v-model="BFeature_selected">
      <option
        v-for="feature in features"
        v-bind:value="{ id: feature.AccountCode, text: feature.AccountNameEN }"
        v-bind:key="feature.AccountCode"
      >
        {{ feature.AccountNameEN }}
      </option>
    </select>
  </div>
  <Chart :series="series" :key="chartComponentKey" :x_title="xTitle" :y_title="yTitle"></Chart>
</template>
<style>
select {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  font-size: 16px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 20px;
  width: 100px;
}
</style>


<script>
import axios from "axios";
import Chart from "@/components/Chart.vue";

export default {
  name: "Home",
  components: {
    Chart
  },
  data() {
    return {
      Sectors_uri: "https://mka-api.alpha.lab.ai/sector/",
      Feature_uri: "https://mka-api.alpha.lab.ai/feature/",
      Axis_uri: "https://mka-api.alpha.lab.ai/finance_by_sector?",
      sector_selected: 0,
      AFeature_selected: "A",
      BFeature_selected: "A",
      series:[],
      XAxis:[],
      YAxis:[],
      sectors: [],
      features: [],
      chartComponentKey:0,
      xTitle:"x",
      yTitle:"y",
    };
  },

  watch: {
    AFeature_selected: function () {
      this.getXAxisValue();
    },
    BFeature_selected: function () {
      this.getYAxisValue();
    },
  },

  beforeMount() {
    this.getSectors();
    this.getFeatures();
  },
  methods: {
    getSectors() {
      axios
        .get(this.Sectors_uri)
        .then((response) => {
          this.sectors = response.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getFeatures() {
      axios
        .get(this.Feature_uri)
        .then((response) => {
          this.features = response.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getXAxisValue(){
      this.xTitle = this.AFeature_selected.text;
      let axis_uri = this.Axis_uri + `sector_id=${this.sector_selected}&feature_id=${this.AFeature_selected.id}`;
      axios
        .get(axis_uri)
        .then((response) => {
          this.XAxis = response.data;
          if (this.YAxis.length > 0)
            this.pinPoint();
        })
        .catch((err) => {
          console.log(err);
        });
        this.chartComponentKey ++;
    },
    getYAxisValue(){
      
      this.yTitle = this.BFeature_selected.text;
      
      let axis_uri = this.Axis_uri + `sector_id=${this.sector_selected}&feature_id=${this.BFeature_selected.id}`;
      axios
        .get(axis_uri)
        .then((response) => {
          this.YAxis = response.data;
          if (this.XAxis.length > 0)
            this.pinPoint();
        })
        .catch((err) => {
          console.log(err);
        });
        this.chartComponentKey ++;
    },
    pinPoint(){
      this.series = [];
      let index;
      for (index = 0; index < this.XAxis.length; index++) {
        this.series.push({
          name: this.XAxis[index].SecuritySymbol,
          data: [[this.XAxis[index].AccumulatedAmount, this.YAxis[index].AccumulatedAmount]]
        });
      }
      this.chartComponentKey ++;
    }
  },
};
</script>
