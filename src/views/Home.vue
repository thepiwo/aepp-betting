<template>
  <div>
    <h1 class="h1">Dice Bet Æpp</h1>
    <Dice />
  </div>
</template>

<script>
  import aeternity from "../utils/aeternity";
  import Dice from "../components/Dice.vue";

  export default {
    name: 'Home',
    components: {Dice},
    data() {
      return {
        address: null,
        balance: null
      };
    },

    async mounted() {
      await aeternity.initClient();

      if (aeternity.isTestnet() && aeternity.balance <= 5) {
        await axios.post(`https://testnet.faucet.aepps.com/account/${aeternity.address}`, {}, {headers: {'content-type': 'application/x-www-form-urlencoded'}}).catch(console.error);
      }
      this.address = aeternity.address;
      this.balance = aeternity.balance;
    },
  };
</script>

<style scoped>

</style>
