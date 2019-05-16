<template>
  <div id="app">
    <h3> {{value_2[0] | moment("dddd, MMMM Do YYYY") }} -> {{value_2[1] | moment("dddd, MMMM Do YYYY") }}  </h3>
    <vue-slider v-model="value_2" :min="fromTimestamp" :max="untilTimestamp" v-on:change="sliderChanged"></vue-slider>

    {{ getHumidity() }}
    <h4>Humidity</h4>
    <Humidity/>

    <h4>Temperature</h4>
    <Temperature/>

    <h4>CO</h4>
    <CO/>
  </div>
</template>

<script>
import Humidity from "./components/Humidity.vue";
import Temperature from "./components/Temperature.vue";
import CO from "./components/CO.vue";
import Firebase from "firebase";
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/antd.css'

var firebaseApp = Firebase.initializeApp({
  apiKey: "AIzaSyCq0F6t5FTJm5Z1ObRzJOqgXk_N2uqHlEg",
  projectId: "officeairquality-51446",
  databaseURL: "https://officeairquality-51446.firebaseio.com"
});
const firestore = firebaseApp.firestore();

export default {
  name: "app",
  components: {
    Humidity,
    Temperature,
    CO,
    VueSlider
  },
  data() {
    return {
      readings: [],
      lowerBoundVal: 0,
      upperBoundVal: 200,
      value_2: [0, 50],
      fromTimestamp: 0,
      untilTimestamp: 86400000
    };
  },
  mounted() {
    this.$data.fromTimestamp  =  1557926884000
    this.$data.untilTimestamp = new Date().getTime()

    this.$binding("readings", firestore.collection("readings")).then(readings => {
      // console.log(readings); 
    });
  },
  methods: {
    sliderChanged: function(event) {
      // console.log(event);
    },
    getHumidity: function() {
      var labels = []
      var humidity = []

      for(var reading of this.$data.readings) {
          if(reading.timestamp > this.$data.fromTimestamp && reading.timestamp < this.$data.untilTimestamp) {
            labels.push( labels.length + 1 );
            humidity.push( reading.humidity ) ;
          }
      }

      return [labels, humidity]
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
