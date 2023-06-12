<template>
  <div id="containerA"></div>
</template>

<script>
import { Chart } from "@antv/g2";
export default {
  name: "helloworldB",
  props: {
    msg: Object,
  },
  data() {
    return {
      mapData: [
        { month: "Jan", city: "Tokyo", temperature: 7 },
        { month: "Jan", city: "London", temperature: 3.9 },
        { month: "Feb", city: "Tokyo", temperature: 6.9 },
        { month: "Feb", city: "London", temperature: 4.2 },
        { month: "Mar", city: "Tokyo", temperature: 9.5 },
        { month: "Mar", city: "London", temperature: 5.7 },
        { month: "Apr", city: "Tokyo", temperature: 14.5 },
        { month: "Apr", city: "London", temperature: 8.5 },
        { month: "May", city: "Tokyo", temperature: 18.4 },
        { month: "May", city: "London", temperature: 11.9 },
        { month: "Jun", city: "Tokyo", temperature: 21.5 },
        { month: "Jun", city: "London", temperature: 15.2 },
        { month: "Jul", city: "Tokyo", temperature: 25.2 },
        { month: "Jul", city: "London", temperature: 17 },
        { month: "Aug", city: "Tokyo", temperature: 26.5 },
        { month: "Aug", city: "London", temperature: 16.6 },
        { month: "Sep", city: "Tokyo", temperature: 23.3 },
        { month: "Sep", city: "London", temperature: 14.2 },
        { month: "Oct", city: "Tokyo", temperature: 18.3 },
        { month: "Oct", city: "London", temperature: 10.3 },
        { month: "Nov", city: "Tokyo", temperature: 13.9 },
        { month: "Nov", city: "London", temperature: 6.6 },
        { month: "Dec", city: "Tokyo", temperature: 9.6 },
        { month: "Dec", city: "London", temperature: 4.8 },
      ],
    };
  },
  mounted() {
    this.initG2();
  },
  methods: {
    initG2() {
      this.chart = new Chart({
        container: "containerA",
        height: this.msg.height,
        width: this.msg.width,
      });

      this.chart.data(this.mapData);
      this.chart.scale({
        month: {
          range: [0, 1],
        },
        temperature: {
          nice: true,
        },
      });

      this.chart.tooltip({
        showCrosshairs: true,
        shared: true,
      });

      this.chart.axis("temperature", {
        label: {
          formatter: (val) => {
            return val + " °C";
          },
        },
      });

      this.chart
        .line()
        .position("month*temperature")
        .color("city")
        .shape("smooth");

      this.chart
        .point()
        .position("month*temperature")
        .color("city")
        .shape("circle");

      this.chart.render();
    },
    changeG2() {
      this.chart.changeSize(this.msg.width, this.msg.height - 30);
    },
  },
};
</script>

<style>
</style>