<template>
  <div class="table-auto w-full lg:max-w-full  mt-12 bg-gray-100">
    <h1 class="text-5xl font-bold">Last 5 days history</h1>

    <table class="table-auto w-full shadow-lg my-4">
      <thead>
        <tr>
          <th class="px-4 py-2 w-1/4">Date</th>
          <th class="px-4 py-2 w-1/4">{{baseCurrency}}</th>
          <th class="px-4 py-2 w-1/4">{{quoteCurrency}}</th>
          <th class="px-4 py-2 w-1/4">From today Gains/Losses</th>
        </tr>
      </thead>
      <tbody >
        <tr v-for="(val, key, index) in history" :key="index">
          <td class="border px-4 py-2">{{ key }}</td>
          <td class="border px-4 py-2">{{ value }}</td>
          <td class="border px-4 py-2">{{ val.rate }}</td>
          <td class="border px-4 py-2">{{ val.gainsLosses }}%</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'PriceHistory',
  props: {
    baseCurrency: String,
    quoteCurrency: String,
    date: String,
    history: Object,
    value: String,
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
