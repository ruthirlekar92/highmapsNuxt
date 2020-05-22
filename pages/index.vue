<template>
  <div class="container">
    <div class="row">
      <div class="col-lg-12">
        <h1 class="title">Show Map</h1>
        <h2 class="subtitle">My fabulous Nuxt.js project</h2>
        <b-dropdown split id="dropdown-1" :text="locationLabel" class="hs__dropdown--primary">
          <b-dropdown-item
            v-for="(location, index) in locations"
            @click="findLocation(location)"
            :key="'location_' + index"
          >{{ location.label }}</b-dropdown-item>
        </b-dropdown>
      </div>
      <div class="col-lg-12 mt-3 text-center">
        <div id="map"></div>
      </div>
    </div>
  </div>
</template>

<script>
import * as Highcharts from "highcharts/highmaps";
import { worldMap } from "../js/worldmap";
import { mapInfo } from "~/js/highmaps";
export default {
  components: {},
  data() {
    return {
      worldMap: worldMap,
      mapInfo: mapInfo(),
      locationLabel: "India",
      mapElement: {}
    };
  },
  computed: {
    locations: function() {
      return this.mapInfo.existingLocations.map((country, index) => {
        return {
          label: country.name,
          value: country.code2,
          id: country.code2
        };
      });
    }
  },
  methods: {
    findLocation(arg) {
      this.locationLabel = arg.label;
      this.mapElement.get(arg.id).zoomTo();
    }
  },
  mounted() {
    console.log("existingLocations", this.mapInfo.existingLocations);
    var xy = Highcharts.mapChart("map", {
      chart: {
        map: this.worldMap,
        spacingBottom: 20,
        backgroundColor: "#4a4a4a"
      },

      title: {
        text: ""
      },

      legend: {
        enabled: true,
        align: "left",
        verticalAlign: "bottom",

        floating: true,
        layout: "vertical",
        valueDecimals: 0,
        color: "#a0a0a0",
        itemStyle: {
          "font-size": "16px",
          "font-weight": "normal",
          "font-stretch": "normal",
          "font-style": "normal",
          "line-height": 2.5,
          "letter-spacing": "normal"
        }
      },
      mapNavigation: {
        enabled: true,
        enableButtons: false
      },
      colorAxis: {
        dataClasses: [
          {
            from: -1,
            to: 0,
            color: "#6b6463",
            name: "Non Existing Locations"
          },
          {
            from: 0,
            to: 1,
            color: "#b1a29f",
            name: "Existing Locations"
          }
        ]
      },
      plotOptions: {
        map: {
          nullColor: "#6b6463",
          borderColor: "transparent",
          tooltip: {
            headerFormat: "",
            pointFormat: "{point.name}: <b>{series.name}</b>"
          }
        }
      },
      series: [
        {
          data: this.mapInfo.existingLocations,
          mapData: this.worldMap,
          name: "Existing Locations",
          joinBy: ["iso-a2", "code2"],
          allowPointSelect: true,
          cursor: "pointer"
        }
      ]
    });
    this.mapElement = xy;
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}

#map {
  width: 100%;
}
.highcharts-container {
  width: 100% !important;
}
.highcharts-root {
  width: 100%;
}
</style>
