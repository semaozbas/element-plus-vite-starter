<template>
   <div>
      <div>
         <h3>Currency Conversion</h3>

         <el-select v-model="fromCurrencyCode" placeholder="Select from currency" center size="large" @change="fromCurrency">
            <el-option v-for="currency in currencies" :value="currency" :key="currency" />
         </el-select>
      </div>
   </div>
</template>

<script>
import axios from 'axios';
export default {
   name: 'ConvertCurrency',
   data() {
      return {
         fromCurrencyCode: '',
         fromCurrencyName: '',
         apiKey: 'vtfFWrZHxs28K5x4RK8WJR5sIgTJm7X0',
         currencies: {},
         amount: 1,
         convertedValue: 0,
         symbols: {},
      };
   },
   created() {
      this.getAllCurrencies();
   },

   methods: {
      getAllCurrencies() {
         axios
            .get('https://api.apilayer.com/exchangerates_data/symbols', {
               headers: {
                  apikey: this.apiKey,
               },
            })
            .then((res) => {
               this.symbols = res.data.symbols;
               this.currencies = Object.keys(this.symbols);
            })
            .catch((err) => {
               console.log(err);
            });
      },

      fromCurrency() {
         console.log('this.fromCurrencyCode ' + this.fromCurrencyCode);
         this.fromCurrencyName = this.symbols[this.fromCurrencyCode];
         console.log('this.fromCurrencyName ' + this.fromCurrencyName);
      },
   },
};
</script>

<style></style>
