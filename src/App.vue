<script setup></script>

<template>
  <div>
    <form @submit.prevent="convert">
      <div>
        <label>Value</label>
        <input v-model.number="value" />
      </div>
      <div>
        <label>From Currency</label>
        <select v-model="fromCurrency">
          <option v-for="c in fromCurrencies" :key="c">{{ c }}</option>
        </select>
      </div>
      <div>
        <label>To Currency</label>
        <select v-model="toCurrency">
          <option v-for="c in toCurrencies" :key="c">{{ c }}</option>
        </select>
      </div>
      <button type="submit" :disabled="!formValid">Convert</button>
    </form>
    <div v-if="formValid">
      {{ value }} {{ fromCurrency }} is {{ result.toFixed(2) }} {{ toCurrency }}
    </div>
  </div>
</template>

<script>
const currencies = [
  'EUR',
  'USD',
  'CAD' // Add other currency codes as needed
]
const apiKey = '872798d3a50df2488cc329de45d55c10' // Replace with your actual API key
const apiEndpoint = 'https://api.currencylayer.com/live'
export default {
  name: 'App',
  data() {
    return {
      value: 0,
      fromCurrency: 'EUR',
      toCurrency: 'USD',
      currencies,
      result: 0
    }
  },
  computed: {
    fromCurrencies() {
      return this.currencies.filter((c) => c !== this.toCurrency)
    },
    toCurrencies() {
      return this.currencies.filter((c) => c !== this.fromCurrency)
    },
    formValid() {
      return +this.value >= 0 && this.fromCurrency && this.toCurrency
    }
  },
  methods: {
    async convert() {
      if (!this.formValid) {
        return
      }
      const url = `${apiEndpoint}?access_key=${apiKey}`
      const res = await fetch(url)
      const data = await res.json()

      if (data.success) {
        const conversionRate = data.quotes[`${this.fromCurrency}${this.toCurrency}`]
        this.result = this.value * conversionRate
      } else {
        this.result = 0 // Set a default value if there's an error
      }
    }
  }
}
</script>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
