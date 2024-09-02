<template>
    <div class="d-lg-flex align-items-center mt-5 mt-lg-0 h-100">
        <div class="container">
            <div class="currency-converter">
                <h1 class="my-4">Currency Converter</h1>

                <form @submit.prevent="convertCurrency">
                    <div class="mb-4">
                        <label for="amountFrom" class="form-label">Valor a Converter:</label>
                        <div class="input-group">
                            <input id="amountFrom" v-model="amountFrom" type="number" class="form-control"
                                placeholder="Digite o valor" required />
                            <select id="currencyFrom" v-model="currencyFrom" class="form-select" required>
                                <option v-for="currencyOption in currencyOptions" :key="currencyOption"
                                    :value="currencyOption">
                                    {{ currencyNames[currencyOption] || currencyOption }}
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
                            <input id="amountTo" v-model="result" type="text" class="form-control"
                                placeholder="Resultado" disabled />
                            <select id="currencyTo" v-model="currencyTo" class="form-select" required>
                                <option v-for="currencyOption in currencyOptions" :key="currencyOption"
                                    :value="currencyOption">
                                    {{ currencyNames[currencyOption] || currencyOption }}
                                </option>
                            </select>
                        </div>
                    </div>

                    <button type="submit" class="btn btn-primary">Converter</button>
                </form>

                <div v-if="result" class="mt-4">
                    <h3>
                        Resultado: {{ amountFrom }} {{ currencyNames[currencyFrom] || currencyFrom }} =
                        {{ result }} {{ currencyNames[currencyTo] || currencyTo }}
                    </h3>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import '@/styles/index.scss';

export default {
    data() {
        return {
            amountFrom: null,
            currencyFrom: 'USD',
            currencyTo: 'EUR',
            result: null,
            currencyOptions: ['USD', 'CAD', 'AUD', 'EUR', 'GBP', 'JPY', 'BRL', 'CHF', 'CNY', 'KRW'],
            currencyNames: {
                USD: 'Dólar Americano',
                CAD: 'Dólar Canadense',
                AUD: 'Dólar Australiano',
                EUR: 'Euro',
                GBP: 'Libra Esterlina',
                JPY: 'Iene Japonês',
                BRL: 'Real',
                CHF: 'Franco Suíço',
                CNY: 'Yuan Chinês',
                KRW: 'Won Sul-Coreano'
            }
        };
    },
    watch: {
        currencyFrom() {
            this.result = null;
        },
        currencyTo() {
            this.result = null;
        }
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

<style scoped>
.currency-converter {
    max-width: 500px;
    margin: auto;
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 8px;
    background-color: #f9f9f9;

    h1 {
        font-size: 1.5rem;
        text-align: center;
    }

    .form-label {
        font-weight: bold;
    }

    .btn {
        width: 100%;
    }
}

@media (max-width: 576px) {
    .currency-converter {
        padding: 12px;
    }
}
</style>
