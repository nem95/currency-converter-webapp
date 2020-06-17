<template>
  <div class="table-auto w-full lg:max-w-full  mt-12 bg-gray-100">
    <h1 class="text-5xl font-bold">Last 5 days history</h1>

    <table class="table-auto w-full shadow-lg my-4">
      <thead>
        <tr>
          <th class="px-4 py-2 w-1/3">Date</th>
          <th class="px-4 py-2 w-1/3">{{baseCurrency}}</th>
          <th class="px-4 py-2 w-1/3">{{quoteCurrency}}</th>
        </tr>
      </thead>
      <tbody >
        <tr v-for="(val, key, index) in history" :key="index">
          <td class="border px-4 py-2">{{ key }}</td>
          <td class="border px-4 py-2">{{ value }}</td>
          <td class="border px-4 py-2">{{ val }}</td>
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
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
