<template>
    <div class="currency-converter container">
      <h1 class="my-4">Currency Converter</h1>
      
      <form @submit.prevent="convertCurrency">
        <div class="mb-4">
          <label for="amountFrom" class="form-label">Valor a Converter:</label>
          <div class="input-group">
            <input
              id="amountFrom"
              v-model="amountFrom"
              type="number"
              class="form-control"
              placeholder="Digite o valor"
              required
            />
            <select
              id="currencyFrom"
              v-model="currencyFrom"
              class="form-select"
              required
            >
              <option v-for="currencyOption in currencyOptions" :key="currencyOption" :value="currencyOption">
                {{ currencyOption }}
              </option>
            </select>
          </div>
        </div>
  
        <div class="text-center my-2">
          <button type="button" class="btn btn-sm btn-secondary" @click="invertCurrencies">
            ⇅
          </button>
        </div>
  
        <div class="mb-3">
          <label for="currencyTo" class="form-label">Converter para:</label>
          <div class="input-group">
            <input
              id="amountTo"
              v-model="result"
              type="text"
              class="form-control"
              placeholder="Resultado"
              disabled
            />
            <select
              id="currencyTo"
              v-model="currencyTo"
              class="form-select"
              required
            >
              <option v-for="currencyOption in currencyOptions" :key="currencyOption" :value="currencyOption">
                {{ currencyOption }}
              </option>
            </select>
          </div>
        </div>
  
        <button type="submit" class="btn btn-primary">Converter</button>
      </form>
  
      <div v-if="result" class="mt-4">
        <h3>Resultado: {{ amountFrom }} {{ currencyFrom }} = {{ result }} {{ currencyTo }}</h3>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import '@/styles/main.scss';
  
  export default {
    data() {
      return {
        amountFrom: null,
        currencyFrom: 'USD',
        currencyTo: 'EUR',
        result: null,
        currencyOptions: ['USD', 'EUR', 'GBP', 'JPY', 'BRL', 'CAD', 'AUD', 'CHF', 'CNY']
      };
    },
    methods: {
      async convertCurrency() {
        try {
          const response = await axios.get(`https://v6.exchangerate-api.com/v6/264f2f4ba1526b1548273546/latest/${this.currencyFrom}`);
          const rate = response.data.conversion_rates[this.currencyTo];
          this.result = (this.amountFrom * rate).toFixed(2);
        } catch (error) {
          console.error("Erro ao obter as taxas de câmbio:", error);
        }
      },
      invertCurrencies() {
        const temp = this.currencyFrom;
        this.currencyFrom = this.currencyTo;
        this.currencyTo = temp;
        this.result = null; // Limpa o resultado ao inverter
      }
    }
  };
  </script>
  