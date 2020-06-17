<template>
  <div id="app">
    <h1 class="text-6xl font-black">Money Converter</h1>

    <div class="container mx-auto py-10">
      <div class="w-full lg:max-w-full shadow-lg">
        <div class="flex mb-4 w-full">
          <div class="w-2/5 my-4 mx-4" v-if="currencies">
            <div class="inline-block relative">
              <select
                class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline"
                name="baseCurrency"
                id="baseCurrency"
                @change="changeBaseCurrency($event)"
              >
                <option
                  v-for="(currency, currencyShort, index) in currencies"
                  :key="index"
                  :value="currencyShort"
                  :selected="baseCurrency === currencyShort ? true : false"
                >
                  {{currency}} ({{currencyShort}})
                </option>
              </select>
              <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
              </div>
            </div>
          </div>

          <div class="w-1/5 flex justify-center" v-if="currencies">
            <img src="./assets/img/swap-arrows.svg" alt="" class="h-auto w-4/12 cursor-pointer" @click="swapCurrency">
          </div>
          <div class="w-2/5 my-4 mx-4" v-if="currencies">
            <div class="inline-block relative">
              <select
                class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-4 py-2 pr-8 rounded shadow leading-tight focus:outline-none focus:shadow-outline"
                name="quoteCurrency"
                id="quoteCurrency"
                @change="changeQuoteCurrency($event)">
                <option
                  v-for="(currency, currencyShort, index) in currencies"
                  :key="index"
                  :value="currencyShort"
                  :selected="quoteCurrency === currencyShort ? true : false"
                >
                  {{currency}} ({{currencyShort}})
                </option>
              </select>
              <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
              </div>
            </div>
          </div>
        </div>

        <div class="flex mb-4 w-full flex-col lg:flex-row">
          <div class="w-full px-3 my-4 lg:w-2/5">
            <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2" for="grid-last-name">
              Valeur
            </label>
            <input
              class="appearance-none flex justify-center text-center w-full text-5xl h-32 bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-gray-200"
              type="number"
              placeholder="40"
              v-model="value"
            >
          </div>
          <div class="w-full lg:w-1/5">
          </div>
          <div class="w-full px-3 my-4 lg:w-2/5">
            <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2" for="grid-last-name">
              Résultat
            </label>
            <div
              class="appearance-none flex justify-center items-center text-5xl w-full h-32 bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight"
            >
              <span v-if="converted">{{ converted }}</span>
            </div>
          </div>
        </div>
      </div>

      <PriceHistory v-if="converted" :baseCurrency="baseCurrency" :quoteCurrency="quoteCurrency" :history="rateHistory" :value="value" />
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

import PriceHistory from './components/PriceHistory';
import { getItem, setItem, removeItem } from '../utils/localstorage';

const baseURL = 'http://localhost:7777/';

export default {
  name: 'App',
  components: {
    PriceHistory,
  },
  data() {
    return {
      currencies: null,
      baseCurrency: 'EUR',
      quoteCurrency: 'USD',
      value: 0,
      converted: 0,
      rateHistory: null,
    };
  },
  async created() {
    const currencies = getItem('currencies');
    const history = getItem('history');

    if (currencies){
      this.currencies = currencies;
    } else {
       try {
        const symbols = await axios.get(`${baseURL}symbols`);
        setItem('currencies', symbols.data);
        this.currencies = symbols.data;
      } catch (error) {
        console.log(error);
      }
    }

    if (history) {
      const date = moment().subtract(4, 'd').format('YYYY-MM-DD');

      if (!Object.keys(history).includes(date)) {
        removeItem('history')
      }
    }
    if (!history) {
      try {
        const history = await axios.get(`${baseURL}history`);
        setItem('history', history.data);
        this.rateHistory = history.data;
      } catch (error) {
        console.log(error);
      }
    }

    return;
  },
  watch: {
    value: function () {
      this.converted = "...";
      this.convertCurrency(this.value);
      this.convertedHistory(this.value);
    }
  },
  methods: {
    changeBaseCurrency: function (e) {
      this.baseCurrency = e.target.value;

      if (this.value > 0) {
        this.convertCurrency(this.value);
      }
    },
    changeQuoteCurrency: function (e) {
      this.quoteCurrency = e.target.value;

      if (this.value > 0) {
        this.convertCurrency(this.value);
      }
    },
    swapCurrency: function () {
      const { quoteCurrency, baseCurrency} = this;
      this.quoteCurrency = baseCurrency;
      this.baseCurrency = quoteCurrency;

      if (this.value > 0) {
        this.convertCurrency(this.value);
        this.convertedHistory(this.value);
      }
    },
    convertedHistory: function (value) {
      const history = getItem('history');
      const { quoteCurrency, baseCurrency } = this;
      let convertedHistory = {};

      if (!history) return;

      for (const date in history) {
        const convertedCurrency = (value * history[`${date}`][quoteCurrency]) / history[`${date}`][baseCurrency];

        convertedHistory[date] = convertedCurrency.toFixed(5);
      }

      return this.rateHistory = convertedHistory;
    },
    convertCurrency: async function (value) {
      try {
        const res = await axios.get(`${baseURL}convert?base_currency=${this.baseCurrency}&quote_currency=${this.quoteCurrency}&value=${value}`);

        return this.converted = res.data.toFixed(3);
      } catch (error) {
        console.log(error);
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.currency__selector {
  display: flex;
}
</style>
