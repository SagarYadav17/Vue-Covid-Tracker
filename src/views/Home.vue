<template>
  <main v-if="!loading">
    <CountrySelect @get-country="getCountryData" :countries="countries" />

    <button
      v-if="status.Country"
      class="
        bg-green-700
        text-white
        rounded
        p-3
        mt-5
        mb-10
        focused:outline-none
        hover:bg-green-500
      "
      @click="clearCountryData"
    >
      Clear Country
    </button>

    <DataTitle :text="title" :dataDate="dataDate" />
    <DataBoxes :status="status" />
  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-10 mb-6">Fetching Data</div>
  </main>
</template>

<script>
import DataTitle from "@/components/DataTitle";
import DataBoxes from "@/components/DataBoxes";
import CountrySelect from "@/components/CountrySelect";

export default {
  name: "Home",
  components: { DataTitle, DataBoxes, CountrySelect },
  data() {
    return {
      loading: true,
      title: "Global",
      dataDate: "",
      status: {},
      countries: [],
    };
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch("https://api.covid19api.com/summary");
      const data = await res.json();
      return data;
    },

    getCountryData(country) {
      this.status = country;
      this.title = country.Country;
    },

    async clearCountryData() {
      this.loading = true;
      const data = await this.fetchCovidData();
      this.title = "Global";
      this.status = data.Global;
      this.loading = false;
    },
  },
  async created() {
    const data = await this.fetchCovidData();

    this.dataDate = data.Date;
    this.status = data.Global;
    this.countries = data.Countries;
    this.loading = false;
  },
};
</script>
