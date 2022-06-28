<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card class="logo py-4 d-flex justify-center">
        <NuxtLogo />
        <VuetifyLogo />
      </v-card>
      <v-card>
        <v-card-title class="headline">
          Welcome to the Vuetify + Nuxt.js template
        </v-card-title>
        <v-card-text>
          <button
            class="grid-child"
            id="btn-kiwrious-connect"
            ref="btn_kiwrious_connect"
          >
            Connect Kiwrious
          </button>
          <button
            class="grid-child"
            id="btn-kiwrious-disconnect"
            ref="btn_kiwrious_disconnect"
          >
            Disconnect Kiwrious
          </button>
          <div
            class="rightChartDescription"
            id="kiwrious-value"
            ref="kiwriousValues"
          ></div>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" nuxt to="/inspire"> Continue </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
// import { setWasm, setBinUrl } from "copper3d_plugin_heartjs_config";
// console.log("aaaa", setBinUrl);
// import * as Copper from "gltfloader-plugin-test";

export default {
  name: "IndexPage",
  connect: null,
  disconnect: null,
  kiwriousValue: null,
  Copper: null,

  mounted() {
    // this.Copper = require("gltfloader-plugin-test");
    this.Copper = this.$Copper();
    this.connect = this.$refs.btn_kiwrious_connect;
    this.disconnect = this.$refs.btn_kiwrious_disconnect;
    this.kiwriousValue = this.$refs.kiwriousValues;
    this.startKiwrious();
  },
  methods: {
    startKiwrious() {
      console.log(this.Copper);
      this.Copper.kiwrious.setBinUrl("kiwrious-config/prog.bin");
      this.Copper.kiwrious.setWasm("kiwrious-config/libunicorn.out.wasm");

      this.Copper.kiwrious.serialService.onSerialConnection = (isConnected) => {
        console.log(isConnected);
        this.connect.style.display = isConnected ? "none" : "block";
        this.disconnect.style.display = isConnected ? "block" : "none";
      };
      this.connect.onclick = async () => {
        this.connect.disabled = true;
        await this.Copper.kiwrious.serialService.connectAndReadAsync();
        this.connect.disabled = false;
      };
      this.disconnect.onclick = async () => {
        this.disconnect.disabled = true;
        await this.Copper.kiwrious.serialService.disconnectAsync();
        this.disconnect.disabled = false;
      };
      this.Copper.kiwrious.serialService.onSerialData = (decodedData) => {
        const values = decodedData.decodedValues;

        const val = values[0].value;
        const status = val.status;
        const hrVal = val.heartrate;

        this.kiwriousValue.innerText = status;

        if (status === "Ready") {
          this.kiwriousValue.innerText = (hrVal / 2).toString();
        }
      };
    },
  },
};
</script>
