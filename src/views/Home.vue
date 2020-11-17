<template>
  <div class="home">
    <h2>A page with Three dropdown and 1 scatter plot chart</h2>
    <select v-model="sector_selected">
      <option
        v-for="sector in sectors"
        v-bind:value="sector.SectorNumber"
        v-bind:key="sector.SectorNumber"
      >
        {{ sector.SectorNumber }}
      </option>
    </select>
    <select v-model="AFeature_selected">
      <option
        v-for="feature in features"
        v-bind:value="feature.AccountCode"
        v-bind:key="feature.AccountCode"
      >
        {{ feature.AccountCode }}
      </option>
    </select>
    <select v-model="BFeature_selected">
      <option
        v-for="feature in features"
        v-bind:value="feature.AccountCode"
        v-bind:key="feature.AccountCode"
      >
        {{ feature.AccountCode }}
      </option>
    </select>
  </div>
  <Chart :dots="dots" :key="chartComponentKey"></Chart>
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
      dots:[],
      XAxios:0,
      YAxios:0,
      sectors: [
        {
          SectorIndex: null,
          SectorSymbol: "N/A",
          SectorNameEN: "N/A",
          SectorNumber: 0,
          SectorNameTH: "N/A",
          IndustryNumber: 0,
        },
        {
          SectorIndex: 1034,
          SectorSymbol: "AGRI",
          SectorNameEN: "Agribusiness",
          SectorNumber: 1,
          SectorNameTH: "ธุรกิจการเกษตร",
          IndustryNumber: 1,
        },
        {
          SectorIndex: 1035,
          SectorSymbol: "BANK",
          SectorNameEN: "Banking",
          SectorNumber: 2,
          SectorNameTH: "ธนาคาร",
          IndustryNumber: 3,
        },
        {
          SectorIndex: 1036,
          SectorSymbol: "CONMAT",
          SectorNameEN: "Construction Materials",
          SectorNumber: 3,
          SectorNameTH: "วัสดุก่อสร้าง",
          IndustryNumber: 5,
        },
        {
          SectorIndex: 1037,
          SectorSymbol: "PETRO",
          SectorNameEN: "Petrochemicals & Chemicals",
          SectorNumber: 4,
          SectorNameTH: "ปิโตรเคมีและเคมีภัณฑ์",
          IndustryNumber: 4,
        },
        {
          SectorIndex: 1038,
          SectorSymbol: "COMM",
          SectorNameEN: "Commerce",
          SectorNumber: 5,
          SectorNameTH: "พาณิชย์",
          IndustryNumber: 7,
        },
        {
          SectorIndex: 1039,
          SectorSymbol: "ICT",
          SectorNameEN: "Information & Communication Technology",
          SectorNumber: 6,
          SectorNameTH: "เทคโนโลยีสารสนเทศและการสื่อสาร",
          IndustryNumber: 8,
        },
        {
          SectorIndex: null,
          SectorSymbol: "N/A",
          SectorNameEN: "N/A",
          SectorNumber: 7,
          SectorNameTH: "ไม่ระบุ",
          IndustryNumber: 8,
        },
        {
          SectorIndex: 1040,
          SectorSymbol: "ETRON",
          SectorNameEN: "Electronic Components",
          SectorNumber: 8,
          SectorNameTH: "ชิ้นส่วนอิเล็กทรอนิกส์",
          IndustryNumber: 8,
        },
        {
          SectorIndex: 1041,
          SectorSymbol: "ENERG",
          SectorNameEN: "Energy & Utilities",
          SectorNumber: 9,
          SectorNameTH: "พลังงานและสาธารณูปโภค",
          IndustryNumber: 6,
        },
        {
          SectorIndex: 1042,
          SectorSymbol: "MEDIA",
          SectorNameEN: "Media & Publishing",
          SectorNumber: 10,
          SectorNameTH: "สื่อและสิ่งพิมพ์",
          IndustryNumber: 7,
        },
        {
          SectorIndex: 1043,
          SectorSymbol: "FIN",
          SectorNameEN: "Finance & Securities",
          SectorNumber: 11,
          SectorNameTH: "เงินทุนและหลักทรัพย์",
          IndustryNumber: 3,
        },
        {
          SectorIndex: 1044,
          SectorSymbol: "FOOD",
          SectorNameEN: "Food & Beverage",
          SectorNumber: 12,
          SectorNameTH: "อาหารและเครื่องดื่ม",
          IndustryNumber: 1,
        },
        {
          SectorIndex: 1045,
          SectorSymbol: "HELTH",
          SectorNameEN: "Health Care Services",
          SectorNumber: 13,
          SectorNameTH: "การแพทย์",
          IndustryNumber: 7,
        },
        {
          SectorIndex: 1046,
          SectorSymbol: "TOURISM",
          SectorNameEN: "Tourism & Leisure",
          SectorNumber: 14,
          SectorNameTH: "การท่องเที่ยวและสันทนาการ",
          IndustryNumber: 7,
        },
        {
          SectorIndex: 1047,
          SectorSymbol: "HOME",
          SectorNameEN: "Home & Office Products",
          SectorNumber: 15,
          SectorNameTH: "ของใช้ในครัวเรือนและสำนักงาน",
          IndustryNumber: 2,
        },
      ],
      features: [
        { AccountCode: 110100, AccountNameEN: "CASH AND CASH EQUIVALENTS" },
        { AccountCode: 110201, AccountNameEN: "DOMESTIC - INTEREST BEARING" },
        {
          AccountCode: 110202,
          AccountNameEN: "DOMESTIC - NON-INTEREST BEARING",
        },
        { AccountCode: 110203, AccountNameEN: "FOREIGN - INTEREST BEARING" },
        {
          AccountCode: 110204,
          AccountNameEN: "FOREIGN - NON-INTEREST BEARING",
        },
        {
          AccountCode: 110299,
          AccountNameEN: "INTERBANK AND MONEY MARKET ITEMS",
        },
        {
          AccountCode: 110300,
          AccountNameEN: "LOANS TO FINANCIAL INSTITUTIONS",
        },
        {
          AccountCode: 110400,
          AccountNameEN: "SECURITIES PURCHASED UNDER RESALE AGREEMENTS",
        },
        { AccountCode: 110501, AccountNameEN: "HELD-FOR-TRADING INVESTMENTS" },
        { AccountCode: 110502, AccountNameEN: "LONG-TERM INVESTMENTS" },
        { AccountCode: 110513, AccountNameEN: "INVESTMENT AT FAIR VALUE" },
        { AccountCode: 110514, AccountNameEN: "OTHER INVESTMENTS" },
        {
          AccountCode: 110515,
          AccountNameEN: "INVESTMENTS IN SECURITIES AT FAIR VALUE",
        },
        { AccountCode: 110520, AccountNameEN: "AVAILABLE-FOR-SALE INVESMENTS" },
        { AccountCode: 110521, AccountNameEN: "HELD-TO-MATURITY INVESTMENTS" },
        { AccountCode: 110522, AccountNameEN: "OTHER INVESTMENTS" },
        {
          AccountCode: 110581,
          AccountNameEN: "LESS : ALLOWANCE FOR IMPAIRMENT OF INVESTMENTS",
        },
        {
          AccountCode: 110591,
          AccountNameEN: "LESS : ALLOWANCE FOR REVALUATION",
        },
        { AccountCode: 110599, AccountNameEN: "INVESTMENTS - NET" },
        { AccountCode: 110600, AccountNameEN: "SHORT-TERM INVESTMENTS" },
        { AccountCode: 110701, AccountNameEN: "OTHER PARTIES" },
        { AccountCode: 110702, AccountNameEN: "RELATED PARTIES" },
        {
          AccountCode: 110799,
          AccountNameEN: "TRADE ACCOUNTS AND OTHER RECEIVABLE",
        },
        {
          AccountCode: 110800,
          AccountNameEN:
            "INVESTMENT IN ASSOCIATES JOINT VENTURES AND/OR JOINTLY-CONTROL ENTITIES  EQUITY METHOD",
        },
        {
          AccountCode: 110900,
          AccountNameEN: "INVESTMENT ACCOUNTED FOR USING COST METHOD",
        },
        { AccountCode: 110901, AccountNameEN: "RELATED PARTIES" },
        { AccountCode: 110904, AccountNameEN: "OTHER PARTIES" },
        { AccountCode: 110999, AccountNameEN: "ADVANCES AND SHORT-TERM LOANS" },
        {
          AccountCode: 111100,
          AccountNameEN: "RECEIVABLES FROM CLEARING HOUSE",
        },
        { AccountCode: 111101, AccountNameEN: "FINISHED GOODS" },
        { AccountCode: 111102, AccountNameEN: "GOODS IN TRANSIT" },
        { AccountCode: 111103, AccountNameEN: "REAL ESTATE DEVELOPMENT COSTS" },
        { AccountCode: 111104, AccountNameEN: "REAL ESTATE FOR SALES" },
        { AccountCode: 111105, AccountNameEN: "WORK IN PROGRESS" },
      ],
      chartComponentKey:0,
    };
  },

  watch: {
    // sector_selected: function () {
    // },
    AFeature_selected: function () {
      this.getXAxisValue();
      this.pinPoint();
    },
    BFeature_selected: function () {
      this.getYAxisValue();
      this.pinPoint();
    },
  },

  beforeMount() {
    this.getSectors();
    this.getFeatures();
  },
  methods: {
    getSectors() {
      axios.defaults.headers.common["Access-Control-Allow-Origin"] = "*";
      axios
        .get(this.Sectors_uri)
        .then((response) => {
          this.sectors = response;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getFeatures() {
      axios.defaults.headers.common["Access-Control-Allow-Origin"] = "*";
      axios
        .get(this.Feature_uri)
        .then((response) => {
          this.features = response;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getXAxisValue(){
      let axis_uri = this.Axis_uri + `sector_id=${this.sector_selected}&feature_id=${this.AFeature_selected}`;
      axios
        .get(axis_uri)
        .then((response) => {
          this.XAxis = response.AccumulateAmount;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getYAxisValue(){
      let axis_uri = this.Axis_uri + `sector_id=${this.sector_selected}&feature_id=${this.BFeature_selected}`;
      axios
        .get(axis_uri)
        .then((response) => {
          this.YAxis = response.AccumulateAmount;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    pinPoint(){
      this.dots.push([this.XAxios, this.YAxios]);
      this.chartComponentKey ++;
    }
  },
};
</script>
