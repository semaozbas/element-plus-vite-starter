<template>
   <div>
      <div>
         <h3>Currency Conversion</h3>

         <div>
            <el-date-picker v-model="selectedDate" type="date" format="DD-MM-YYYY" placeholder="Pick a date" @change="updatestartDate()" />
         </div>

         <div>
            <el-input-number v-model="amount" :min="1" size="large" @change="handleChange" />
            <el-select v-model="fromCurrencyCode" placeholder="Select from currency" center size="large" @change="fromCurrency">
               <el-option v-for="currency in currencies" :value="currency" :key="currency" />
            </el-select>
         </div>

         <div>
            <el-input-number v-model="convertedValue" disabled size="large" />
            <el-select v-model="toCurrencyCode" placeholder="Select to currency" size="large" @change="toCurrency">
               <el-option v-for="currency in currencies" :value="currency" :key="currency" />
            </el-select>
         </div>
      </div>
   </div>
</template>

<script>
//css eklendi
import axios from 'axios';
export default {
   name: 'ConvertCurrency',
   data() {
      return {
         fromCurrencyCode: '',
         fromCurrencyName: '',
         toCurrencyCode: '',
         toCurrencyName: '',
         apiKey: 'vtfFWrZHxs28K5x4RK8WJR5sIgTJm7X0',
         selectedDate: new Date().toISOString().substr(0, 10),
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

      convertCurrency() {
         axios
            .get('https://api.apilayer.com/exchangerates_data/convert', {
               headers: {
                  apikey: this.apiKey,
               },
               params: {
                  to: this.toCurrencyCode,
                  from: this.fromCurrencyCode,
                  amount: this.amount,
                  date: this.selectedDate,
               },
            })
            .then((res) => {
               this.convertedValue = res.data.result;

               console.log('convertCurrency ' + this.convertedValue);
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
      toCurrency() {
         console.log('this.toCurrencyCode ' + this.toCurrencyCode);
         this.toCurrencyName = this.symbols[this.toCurrencyCode];
         console.log('this.toCurrencyName ' + this.fromCurrencyName);
      },
      updatestartDate() {
         const date = new Date(this.selectedDate);
         date.setDate(date.getDate() + 1);
         this.selectedDate = new Date(date).toISOString().substr(0, 10);
         console.log(this.selectedDate);
      },
      handleChange() {
         console.log('this.amount' + this.amount);
      },
   },
};
</script>

<style></style>
