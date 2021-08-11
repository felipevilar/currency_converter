<template>
  <v-container class="mt-5">
    <v-row>
      <v-col cols="10">
        <v-text-field
          v-model.number="defaultSelection.value"
          placeholder="Insira um valor aqui..."
          label="Você envia"
          outlined
          type="currency"
        ></v-text-field>
      </v-col>
      <v-col cols="2">
        <v-select
          :items="items"
          v-model="defaultSelection.text"
          label="Escolha uma moeda"
          outlined
        >
          <template slot="prepend-item">
            <p class="text-center pt-2 grey--text">Escolha a moeda</p>
            <v-divider></v-divider>
          </template>

          <template slot="item" slot-scope="data">
            <v-card width="50" height="50" flat>
              <v-img :src="require(`@/assets/${data.item.image}`)"></v-img>
            </v-card>
            <p class="text-center pt-2 mx-2 grey--text">{{ data.item.text }}</p>
          </template>

          <template slot="selection" slot-scope="data">
            <v-card width="50" height="50" flat>
              <v-img :src="require(`@/assets/${data.item.image}`)"></v-img>
            </v-card>
            <p class="text-center pt-2 mx-2 grey--text">{{ data.item.text }}</p>
          </template>
        </v-select>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12">
        <v-stepper vertical width="500px">
          <v-stepper-step step="">
            {{ defaultSelection.text }} 1 =
            {{ unitValue(defaultSelection.text, defaultConverted.text) }}
            {{ defaultConverted.text }}
          </v-stepper-step>
          <v-stepper-content step=""></v-stepper-content>
          <v-stepper-step step="">
            IOF (1,10%) = {{ defaultSelection.text }} {{ iof }}
          </v-stepper-step>
          <v-stepper-content step=""></v-stepper-content>
          <v-stepper-step step="">
            Taxa administrativa = {{ defaultSelection.text }} {{ taxes }}
          </v-stepper-step>
          <v-stepper-content step=""></v-stepper-content>
        </v-stepper>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="10">
        <v-text-field
          v-model.number="result"
          label="Beneficiário recebe"
          placeholder="Insira um valor aqui..."
          outlined
          readonly
        ></v-text-field>
      </v-col>
      <v-col cols="2">
        <v-select
          :items="items"
          v-model="defaultConverted.text"
          label="Escolha uma moeda"
          outlined
        >
          <template slot="prepend-item">
            <p class="text-center pt-2 grey--text">Escolha a moeda</p>
            <v-divider></v-divider>
          </template>

          <template slot="item" slot-scope="data">
            <v-card width="50" height="50" flat>
              <v-img :src="require(`@/assets/${data.item.image}`)"></v-img>
            </v-card>
            <p class="text-center pt-2 mx-2 grey--text">{{ data.item.text }}</p>
          </template>

          <template slot="selection" slot-scope="data">
            <v-card width="50" height="50" flat>
              <v-img :src="require(`@/assets/${data.item.image}`)"></v-img>
            </v-card>
            <p class="text-center pt-2 mx-2 grey--text">{{ data.item.text }}</p>
          </template>
        </v-select>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "Converter",

  data: () => ({
    rates: {
      usdToBrl: 5.2164,
      eurToBrl: 6.397,
      eurToUsd: 1.2206,
      IOF: 0.011,
      FX: 0.01,
    },
    defaultSelection: {
      text: "BRL",
      value: null,
    },
    defaultConverted: {
      text: "USD",
      value: null,
    },
    items: [
      { text: "BRL", image: "br.png" },
      { text: "USD", image: "eua.png" },
      { text: "EUR", image: "eur.png" },
    ],
  }),

  computed: {
    result() {
      return this.fx_rate(
        this.defaultSelection.value,
        this.defaultSelection.text,
        this.defaultConverted.text
      );
    },
    iof() {
      if (this.defaultSelection)
        return (this.defaultSelection.value * this.rates["IOF"]).toFixed(2);
      return 0;
    },
    taxes() {
      if (this.defaultSelection)
        return (this.defaultSelection.value * this.rates["FX"]).toFixed(2);

      return 0;
    },
  },
  methods: {
    fx_rate(value, currency1, currency2) {
      let result;
      let vlr = value - this.iof - this.taxes;
      if (currency1 === "BRL") {
        if (currency2 === "USD") result = vlr / this.rates["usdToBrl"];
        if (currency2 === "EUR") result = vlr / this.rates["eurToBrl"];
      } else if (currency1 === "USD") {
        if (currency2 === "BRL") result = vlr * this.rates["usdToBrl"];
        if (currency2 === "EUR") result = vlr / this.rates["eurToUsd"];
      } else if (currency1 === "EUR") {
        if (currency2 === "BRL") result = vlr * this.rates["eurToBrl"];
        if (currency2 === "USD") result = vlr * this.rates["eurToUsd"];
      }

      if (result) return result.toFixed(3);
      return value;
    },
    unitValue(currency1, currency2) {
      let result;
      if (currency1 === "BRL") {
        if (currency2 === "USD") result = (1/this.rates["usdToBrl"]);
        if (currency2 === "EUR") result = (1/this.rates["eurToBrl"]);
      } else if (currency1 === "USD") {
        if (currency2 === "BRL") result = this.rates["usdToBrl"];
        if (currency2 === "EUR") result = (1/this.rates["eurToUsd"]);
      } else if (currency1 === "EUR") {
        if (currency2 === "BRL") result = this.rates["eurToBrl"];
        if (currency2 === "USD") result = this.rates["eurToUsd"];
      }
      if(result) return result.toFixed(4);
      return 1;
    },
  },
};
</script>
