<template>
  <div class="container mx-auto px-4">
    <div class="pt-8">
      <h1 class="mb-8 text-4xl font-bold text-gray-700">
        Indian Corona Virus Stats
      </h1>
      <div
        class="grid gap-2 grid-cols-1 sm:grid-cols-1 md:grid-cols-2 lg:grid-cols-4 xl:grid-cols-4"
      >
        <div class="p-8 shadow rounded bg-red-500">
          <p class="text-6xl text-white">{{ stats.confirmed }}</p>
          <p class="text-gray-100 text-xl">Infected</p>
        </div>
        <div class="p-8 shadow rounded bg-indigo-500">
          <p class="text-6xl text-white">{{ stats.active }}</p>
          <p class="text-gray-100 text-xl">Active Cases</p>
        </div>
        <div class="p-8 shadow rounded bg-green-500">
          <p class="text-6xl text-white">{{ stats.recovered }}</p>
          <p class="text-gray-100 text-xl">Recovered</p>
        </div>
        <div class="p-8 shadow rounded bg-gray-500">
          <p class="text-6xl text-white">{{ stats.deaths }}</p>
          <p class="text-gray-100 text-xl">Deaths</p>
        </div>
      </div>
      <p class="my-4 text-gray-700 text-right">
        Last Updated on
        <span class="bg-red-200 p-1 rounded">{{ stats.lastupdatedtime }}</span>
      </p>
    </div>
    <br />
    <div v-if="showChart">
      <apexchart
        type="area"
        height="350"
        width="100%"
        :options="chartOptions"
        :series="series"
      ></apexchart>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showChart: false,
      stats: [],
      series: [
        {
          name: "Infected",
          data: []
        },
        {
          name: "Recovered",
          data: []
        },
        {
          name: "Deaths",
          data: []
        }
      ],
      chartOptions: {
        xaxis: {
          type: "category",
          labels: {
            show: false
          }
        },
        toolbar: {
          show: true,
          offsetX: 0,
          offsetY: 0,
          tools: {
            download: true,
            selection: true,
            zoom: true,
            zoomin: true,
            zoomout: true,
            pan: false,
            reset: false,
            customIcons: []
          },
          autoSelected: "zoom"
        },
        chart: {
          height: 350,
          type: "area"
        },
        dataLabels: {
          enabled: false
        },
        stroke: {
          curve: "smooth"
        },
        tooltip: {
          x: {
            format: "dd/MM/yy HH:mm"
          }
        },
        colors: ["#f56565", "#48bb78", "#a0aec0"]
      }
    };
  },
  created() {
    this.$axios
      .get("https://api.covid19india.org/data.json")
      .then(response => {
        this.stats = response.data.statewise[0];
        this.series[0].data = response.data.cases_time_series.map(
          item => item.dailyconfirmed
        );
        this.series[1].data = response.data.cases_time_series.map(
          item => item.dailyrecovered
        );
        this.series[2].data = response.data.cases_time_series.map(
          item => item.dailydeceased
        );
        this.showChart = true;
      })
      .catch(err => {
        console.log(err);
      });
  }
};
</script>
