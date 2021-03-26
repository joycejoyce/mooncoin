<template>
  <div>
    <div class="container">
      <a href="https://mooncoins.net"><img class="logo" src="@/assets/logo.svg" alt="logo"></a>

      <form @submit.prevent="handleSubmitForm">
        <div class="form-group">
          <label for="crpto">Crypto:</label>
          <div class="form-group-inner">
            <input type="text" class="crypto" v-model="crypto.name" placeholder="BTC" required>
            <button class="add">Add</button>
          </div>
          <p class="info">Use Abbreviations</p>
        </div>
      </form>

      <div class="crypto-container">
        <div class="crypts" v-for="(item, name) in CryptosList" :key="item._id">
          <p class="price">${{ formatPrice(item.USD) }}</p>
          <div class="lower">
            <p class="crypto-label">{{ name }}</p>
            <button class="delete-btn" @click.prevent="deleteCrypto(name)"><img src="@/assets/delete.png" alt="delete crypto"></button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    data() {
      return {
        baseApiURL: 'http://localhost:4000/api',
        crypto: {
          name: ''
        },
        Cryptos: [],
        CryptosList: {}
      }
    },
    created() {
      this.getCryptos()
    },
    methods: {
      getCryptos() {
        axios.get(this.baseApiURL).then(res => {
          this.Cryptos = res.data;
          
          let names = Array.prototype.map.call(this.Cryptos, s => s.name).toString();
          console.log(names);

          axios.get(`https://min-api.cryptocompare.com/data/pricemulti?fsyms=${names}&tsyms=USD&api_key=b9dec859a034769d584c9672e198e86aca6db3016958b441f1a9c997fe970102`).then(res => {
            this.CryptosList = res.data
            console.log(this.CryptosList)
          }).catch(error => {
            console.log(error)
          })
        })
      },
      handleSubmitForm() {
        axios.post(this.baseApiURL+'/add-crypto', this.crypto).then(() => {
          console.log(this.crypto)
          this.crypto = {
            name: ''
          }
          this.getCryptos()
        }).catch(error => {
          console.log(error)
        })
      },
      deleteCrypto(name) {
        let foundObj = this.Cryptos.find(x => x.name === name)

        let indexOfArrayItem = this.Cryptos.findIndex(i => i._id === foundObj._id);

        axios.delete(`${this.baseApiURL}/delete-crypto/${foundObj._id}`).then(() => {
          this.Cryptos.splice(indexOfArrayItem, 1);

          this.getCryptos()
        }).catch(error => {
          console.log(error)
        });
      },
      formatPrice(value) {
        return value.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1,');
      }
    }
  }
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@200;300;700&display=swap');

  * {
    box-sizing: border-box;
  }

  body {
    background: #24262A;
    color: white;
    margin: 0;
    height: 100vh;
    font-family: 'Poppins';
  }

  .logo {
    width: 10em;
    margin: 3em 0 0;
  }

  .container {
    text-align: center;
    margin: 0 1em;
  }

  .form-group {
    text-align: left;
    width: 300px;
    margin: 7em auto;
  }

  label {
    font-weight: bold;
    margin-bottom: .7em;
    display: block;
  }

  input {
    padding: 1em;
    outline: none;
    border: none;
    border-top-left-radius: .3em;
    border-bottom-left-radius: .3em;
    font-size: 1.3em;
  }

  .form-group-inner {
    display: grid;
    grid-template-columns: 100px auto;
  }

  button.add {
    font-size: 1em;
    font-weight: bold;
    color: white;
    background: #9100FF;
    border: none;
    outline: none;
    padding: 1em 3em;
    border-top-right-radius: .3em;
    border-bottom-right-radius: .3em;
  }

  p.info {
    color: #7794B0;
  }

  .crypto-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(340px, 1fr));
    grid-gap: 1em;
  }

  .crypts {
    background: #32373C;
  }

  .price {
    font-size: 3em;
    font-weight: 200;
    padding: 0 1em;
  }

  .lower {
    background: #292D30;
    padding: 1em;
    position: relative;
    height: 100px;
  }

  .crypto-label {
    text-transform: uppercase;
    font-size: 1.5em;
    font-weight: bold;
    margin-top: -1.5em;
    color: #788591;
  }

  .delete-btn {
    background: none;
    border: none;
    position: absolute;
    right: 1em;
    bottom: 1em;
    cursor: pointer;
    outline: none;
  }
</style>
