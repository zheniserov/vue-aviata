<template>
  <div class="search">
    <div class="container">
      <div class="search__inner">
        <div class="search__filter">
          <div class="search__filter-card">
            <h3>Опции тарифа</h3>
            <FilterOptions v-bind:options="options" v-on:filter="optFilter" />
          </div>
          <div class="search__filter-card">
            <h3>Авиакомпании</h3>
            <FilterOptions v-bind:carrier="carriers" v-on:filter="carriersFilter" />
          </div>
        </div>
        <div class="search__result">
          <Ticket v-for="(result, i) of filterOpt" :key="i" :result="result" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import FilterOptions from "./FilterOptions";
import Ticket from "./Ticket";
import Results from "@/results.json";
export default {
  computed: {
    filterOpt() {

      const map = [];
      this.carriers.forEach(e => {
        if (e.status) {
          map[e.carrier] = e;
        }
      });

      let isStopped = false;
      let isLuggaged = false;
      let isRefund = false;
      this.options.forEach(e => {
        if (!isStopped) {
          isStopped = e.status && e.id == 1;
        }
        if (!isLuggaged) {
          isLuggaged = e.status && e.id == 2;
        }
        if (!isRefund) {
          isRefund = e.status && e.id == 3;
        }
      });

      let result = this.results;
      if (Object.keys(map).length) {
        result = this.results.filter(e => {
          return map[e.validating_carrier];
        });
      }

      result = result
        .filter(e => {
          return !isStopped || !e.itineraries[0][0].stops;
        })
        .filter(e => {
          return (
            !isLuggaged ||
            e.itineraries[0][0].segments[0].baggage_options[0].value > 0
          );
        })
        .filter(e => {
          return !isRefund || e.refundable;
        });

      return result;
    }
  },
  methods: {
    carriersFilter(carrier) {
      return this.carriers.map(item => {
        if (item.carrier === carrier) {
          item.status = !item.status;
        }
      });
    },
    optFilter(id) {
      return this.options.map(item => {
        if (item.id === id) {
          item.status = !item.status;
        }
      });
    }
  },
  components: {
    FilterOptions,
    Ticket
  },
  data() {
    return {
      options: [
        { id: 1, title: "Только прямые", value: 1, status: false },
        { id: 2, title: "Только с багажом", value: "0PC", status: false },
        { id: 3, title: "Только возвратные", code: "refundable", status: false }
      ],
      carriers: [
        { id: 1, title: "Air Astana", carrier: "KC", status: true },
        { id: 2, title: "Uzbekistan Airways", carrier: "HY", status: true },
        { id: 3, title: "Emirates", carrier: "EK", status: true },
        { id: 4, title: "HR", carrier: "HR", status: true },
        { id: 5, title: "Flydubai", carrier: "FZ", status: true },
        { id: 6, title: "S7 Airlines", carrier: "S7", status: true },
        { id: 7, title: "Air Baltic", carrier: "BT", status: true },
        {
          id: 8,
          title: "China Southern Airlines",
          carrier: "CZ",
          status: true
        },
        { id: 9, title: "Aeroflot", carrier: "SU", status: true },
        { id: 10, title: "Belavia", carrier: "B2", status: true },
        { id: 11, title: "SCAT Airlines", carrier: "DV", status: true },
        { id: 12, title: "Turkish Airlines", carrier: "TK", status: true }
      ],
      results: Results.flights
    };
  }
};
</script>

<style scoped>
h3 {
  font-weight: bold;
  font-size: 14px;
  line-height: 20px;
  text-align: start;
  margin: 4px;
  color: #202123;
}
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 15px;
}
.search__inner {
  display: flex;
  justify-content: space-between;
}
.search__filter-card {
  background: #f5f5f5;
  width: 240px;
  padding: 12px;
  margin: 12px 0;
  border-radius: 4px;
  max-height: 340px;
}
</style>