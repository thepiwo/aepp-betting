<template>
    <div>
        <div class="overlay-loader" v-show="showLoading">
            <BiggerLoader></BiggerLoader>
        </div>
        <img :src="diceSvg(side)" id="dice" alt="dice">
        <button @click="placeBet()" v-if="!isRolling">Place Bet</button>
    </div>
</template>
<script>
  import dice1 from "../assets/dice-1.svg";
  import dice2 from "../assets/dice-2.svg";
  import dice3 from "../assets/dice-3.svg";
  import dice4 from "../assets/dice-4.svg";
  import dice5 from "../assets/dice-5.svg";
  import dice6 from "../assets/dice-6.svg";
  import aeternity from "../utils/aeternity";
  import util from "../utils/util";
  import BiggerLoader from "./BiggerLoader";

  export default {
    name: "Dice",
    components: {BiggerLoader},
    data() {
      return {
        interval: null,
        sides: [dice1, dice2, dice3, dice4, dice5, dice6],
        side: this.random(),
        showLoading: false,
        isRolling: false
      }
    },
    methods: {
      random() {
        return Math.ceil(Math.random() * 6)
      },
      diceSvg(side) {
        return this.sides[side - 1];
      },
      async placeBet() {
        this.showLoading = true;
        const session = (await aeternity.contract.methods.get_current_session()).decodedResult;
        console.log(session);
        await aeternity.contract.methods.bet(this.side - 1, {amount: util.aeToAtoms(1).toNumber()}).then(() => {
          this.showLoading = false;
          this.isRolling = true;
          this.interval = setInterval(() => {
            this.side = this.random()
          }, 80);
        });
        await aeternity.client.awaitHeight(session.block_end, {interval: 5000, attempts: 1000});
        clearInterval(this.interval);
        this.isRolling = false;
        var finishedSession = (await aeternity.contract.methods.get_session_by_id(session.session_id)).decodedResult;
        if (finishedSession.winning_number === undefined) {
          finishedSession = (await aeternity.contract.methods.calculate_winners()).decodedResult;
        }
        const winningBet = finishedSession.bets.find(bet => bet.number === finishedSession.winning_number);
        const won = winningBet.player_address === aeternity.address;
        console.log(finishedSession, winningBet, won);
        if (won) console.log("you won")
      }
    },
    created() {
    }
  }
</script>
<style>
    #dice {
        height: 150px;
        display: block;
    }
</style>
