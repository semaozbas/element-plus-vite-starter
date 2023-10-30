<template>
   <div class="form-container">
      <div class="title-container">
         <h3 class="title">Currency Conversion</h3>

         <div class="date-picker">
            <el-date-picker v-model="selectedDate" type="date" format="DD-MM-YYYY" @change="updateDate()" />
         </div>

         <div class="amount-currency">
            <el-input-number v-model="amount" :min="1" size="large" />
            <el-select class="select-container" v-model="fromCurrencyCode" placeholder="Select from currency" center size="large" @change="fromCurrency">
               <el-option v-for="currency in currencies" :value="currency" :key="currency" />
            </el-select>
         </div>

         <div class="to-currency">
            <el-input-number v-model="convertedValue" disabled size="large" />
            <el-select class="select-container" v-model="toCurrencyCode" placeholder="Select to currency" size="large" @change="toCurrency">
               <el-option v-for="currency in currencies" :value="currency" :key="currency" />
            </el-select>
         </div>

         <p class="text" v-if="resultCurrency">{{ amount }} {{ fromCurrencyName }} equals</p>
         <h1 v-if="resultCurrency">{{ convertedValue }} {{ toCurrencyName }}</h1>
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
   computed: {
      resultCurrency: function () {
         if (this.fromCurrencyCode && this.toCurrencyCode && this.amount && this.selectedDate) {
            setTimeout(() => this.convertCurrency(), 500);
            return this.convertedValue != 0 ? true : false;
         }
      },
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
         this.fromCurrencyName = this.symbols[this.fromCurrencyCode];
         console.log('fromCurrencyCode ' + this.fromCurrencyCode + ' and fromCurrencyName ' + this.fromCurrencyName);
      },
      toCurrency() {
         this.toCurrencyName = this.symbols[this.toCurrencyCode];
         console.log('toCurrencyCode ' + this.toCurrencyCode + ' and toCurrencyName ' + this.fromCurrencyName);
      },
      updateDate() {
         const date = new Date(this.selectedDate);
         date.setDate(date.getDate() + 1);
         this.selectedDate = new Date(date).toISOString().substr(0, 10);
         console.log(this.selectedDate);
      },
   },
};
</script>

<style>
.title-container {
   position: relative;

   .title {
      font-size: 26px;
      color: #1114c9;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
   }
}

.date-picker {
   margin-bottom: 20px;
}

.amount-currency {
   align-items: center;
}

.to-currency {
   align-items: center;
}

.text {
   color: #0a0b0b;
   font-size: 20px;
   text-align: center;
}

.select-container {
   padding: 6px 5px 6px 15px;
   vertical-align: middle;
   width: 200px;
   display: inline-block;
   margin: auto;
}

.form-container {
   background-color: rgb(203, 206, 207);
   width: 700px;
   height: 450px;
   position: relative;
}
</style>
