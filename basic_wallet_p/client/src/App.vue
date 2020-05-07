<template>
  <div id="app">
    <h1>Welcome to DA WALLET</h1>
    <p>Enter your user id:</p>
    <input type="text" v-model="id" />
    <button @click="filter">Show data</button>
    <p>
      Current Balance:
      {{
        income.reduce((acc, curr) => acc + curr.amount, 0) -
          expenses.reduce((acc, curr) => acc + curr.amount, 0)
      }}
    </p>
    <h1></h1>
    <h1>Transactions</h1>
    <h1>Expenses</h1>
    <div>
      <div v-for="(tx, idx) in expenses" :key="idx" class="tx">
        <p>{{ tx.amount }}</p>
        to
        <p>{{ tx.recipient }}</p>
      </div>
    </div>
    <h1>Income</h1>
    <div v-for="(tx, idx) in income" :key="idx" class="tx">
      <p>{{ tx.amount }}</p>
      from
      <p>{{ tx.sender }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      id: "",
      chain: [],
      expenses: [],
      income: []
    };
  },
  methods: {
    filter: function() {
      const filtered = this.chain
        .filter(elem => elem.transactions.length > 0)
        .map(tx => tx.transactions)
        .flat()
        .filter(tx => tx.recipient === this.id || tx.sender === this.id);

      this.expenses = filtered.filter(elem => elem.sender === this.id);
      this.income = filtered.filter(elem => elem.recipient === this.id);
    }
  },
  mounted() {
    axios
      .get("http://localhost:5000/chain")
      .then(({ data }) => (this.chain = data.chain))
      .catch(err => console.error(err));
  }
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.tx {
  width: 10vh;
  display: flex;
  align-items: center;
  justify-content: space-around;
  margin: auto;
}
</style>
