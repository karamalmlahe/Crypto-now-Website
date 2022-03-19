<template>
  <div id="app">
    <div class="title">
      <b-card class="mb-2">
        <b-card-title class="d-flex justify-content-center"
          >Cryptocurrency Now</b-card-title
        >
        <b-card-text class="d-flex justify-content-center">
          Simple app to see cryptocurrency prices live
        </b-card-text>
        <b-card-text class="d-flex justify-content-center">
          <h6 class="d-flex align-items-end mx-2">Made With 💖 By</h6>
          <a href="https://instagram.com/karam_almlahe" target="_blank">
            <b-avatar
              v-b-tooltip.hover.bottom
              title="Karam"
              src="https://cdn-icons-png.flaticon.com/512/147/147140.png"
          /></a>
        </b-card-text>
        <div class="row">
          <div class="col-md-6">
            <b-button v-b-toggle.collapse-1 class="my-2 w-100"
              >Cryptocurrency Filter ⚙️</b-button
            >
            <b-collapse id="collapse-1">
              <b-card>
                <h5 class="card-text">Cryptocurrency Filter ⚙️ :</h5>
                <b-input
                  v-on:keyup.enter="AddCryptocurrency()"
                  placeholder="Enter a cryptocurrency name"
                  v-model="CryptoIput"
                />
                <b-list-group class="my-1">
                  <b-list-group-item
                    class="d-flex justify-content-between align-items-center"
                    v-for="(Crypto, index) in Cryptocurrency"
                    :key="Crypto"
                    >{{ Crypto }}
                    <b-button
                      variant="link"
                      @click="RemoveCryptocurrency(index)"
                      >Remove</b-button
                    >
                  </b-list-group-item>
                </b-list-group>
              </b-card>
            </b-collapse>
          </div>
          <div class="col-md-6">
            <b-button class="my-2 w-100" v-b-toggle.collapse-2
              >Coins Filter 💸</b-button
            >
            <b-collapse id="collapse-2">
              <b-card>
                <h5 class="card-text">Coins Filter 💸 :</h5>
                <b-input
                  v-on:keyup.enter="AddCoin()"
                  placeholder="Enter a coin name"
                  v-model="CoinInput"
                />
                <b-list-group class="my-1">
                  <b-list-group-item
                    class="d-flex justify-content-between align-items-center"
                    v-for="(Coin, index) in Coins"
                    :key="Coin"
                    >{{ Coin }}
                    <b-button variant="link" @click="RemoveCoin(index)"
                      >Remove</b-button
                    >
                  </b-list-group-item>
                </b-list-group>
              </b-card>
            </b-collapse>
          </div>
        </div>
      </b-card>
    </div>
    <div class="coins">
      <b-card class="mb-2 my-5">
        <h5>On Live 🔴 | Refresh every 1 second</h5>
        <div class="row d-flex justify-content-around">
          <b-card
            v-for="(result, index) in results"
            :key="result[index]"
            class="col-xl-3 col-sm-5 hover my-2 mx-1"
          >
            <b-card-title class="d-flex justify-content-center">{{
              index
            }}</b-card-title>
            <hr id="line" />
            <b-card-text
              class="d-flex justify-content-center"
              v-for="Coin in Coins"
              :key="Coin"
              ><div>{{ Coin + " : " + result[Coin] }}</div></b-card-text
            >
          </b-card>
        </div>
      </b-card>
      <b-modal ref="bv-modal" id="bv-modal" hide-footer centered hide-header>
        <div class="d-block text-center">
          <h5>{{ message }}</h5>
        </div>
        <b-button
          class="mt-3 bg-warning w-100"
          @click="$bvModal.hide('bv-modal')"
          >OK</b-button
        >
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "app",
  data: () => ({
    message: null,
    CoinInput: null,
    CryptoIput: null,
    Cryptocurrency: [
      "BTC",
      "ETH",
      "BNB",
      "ADA",
      "XRP",
      "DOGE",
      "DOT",
      "UNI",
      "ALPHA",
    ],
    Coins: ["EUR", "USD", "ILS"],
    results: [],
    url: null,
  }),
  mounted() {
    this.getDataFromAPI();
    this.timer = setInterval(this.getDataFromAPI, 1000);
  },
  methods: {
    getDataFromAPI: function () {
      this.url = `https://min-api.cryptocompare.com/data/pricemulti?fsyms=${this.Cryptocurrency}&tsyms=${this.Coins}`;
      axios.get(this.url).then((response) => {
        this.results = response.data;
      });
    },
    CheckCrypto() {
      axios
        .get(
          `https://min-api.cryptocompare.com/data/pricemulti?fsyms=${this.CryptoIput}&tsyms=${this.Coins}`
        )
        .then((response) => {
          if (response.data[this.CryptoIput.toUpperCase()]) {
            this.Cryptocurrency.push(this.CryptoIput.toUpperCase());
            this.getDataFromAPI();
            this.CryptoIput = null;
          } else {
            this.message = "Please enter a correct cryptocurrency name";
            this.$refs["bv-modal"].show();
          }
        });
    },
    CheckCoin() {
      axios
        .get(
          `https://min-api.cryptocompare.com/data/pricemulti?fsyms=${this.Cryptocurrency}&tsyms=${this.CoinInput}`
        )
        .then((response) => {
          if (
            this.CoinInput.split(" ").length == 1 &&
            response.data[this.Cryptocurrency[0]]
          ) {
            this.Coins.push(this.CoinInput.toUpperCase());
            this.getDataFromAPI();
            this.CoinInput = null;
          } else {
            this.message = "Please enter a correct coin name";
            this.$refs["bv-modal"].show();
          }
        });
    },
    AddCryptocurrency() {
      if (!this.Cryptocurrency.includes(this.CryptoIput.toUpperCase())) {
        this.CheckCrypto();
      } else {
        this.message = "The coin is already exist";
        this.$refs["bv-modal"].show();
      }
    },
    RemoveCryptocurrency(index) {
      if (this.Cryptocurrency.length > 1) {
        this.Cryptocurrency.splice(index, 1);
      } else {
        this.message = "The list can't be empty";
        this.$refs["bv-modal"].show();
      }
    },
    AddCoin() {
      if (!this.Coins.includes(this.CoinInput.toUpperCase())) {
        this.CheckCoin();
        // this.Coins.push(this.CoinInput.toUpperCase());
        // this.getDataFromAPI();
        // alert(this.results[this.Cryptocurrency[0]][this.CoinInput.toUpperCase()]);
        // if (!this.results[this.Cryptocurrency[0]][this.CoinInput]) {
        //   this.message = "Please enter a correct coin name";
        //   this.$refs["bv-modal"].show();
        //   this.RemoveCoin(this.Coins.length - 1);
        // }
      } else {
        this.message = "The coin is already exist";
        this.$refs["bv-modal"].show();
      }
    },
    RemoveCoin(index) {
      if (this.Coins.length > 1) {
        this.Coins.splice(index, 1);
      } else {
        this.message = "The list can't be empty";
        this.$refs["bv-modal"].show();
      }
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Cairo&display=swap");
#bv-modal {
  font-family: Cairo;
  cursor: default;
}
#app {
  font-family: Cairo;
  padding: 30px;
  margin: auto;
  max-width: 900px;
  cursor: default;
}

.hover:hover {
  border-color: red;
  #line {
    color: red;
  }
}
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
</style>
