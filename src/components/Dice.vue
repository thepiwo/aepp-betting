<template>
  <img :src="diceSvg(side)" id="dice" @click="roll()">
</template>
<script>
    import dice1 from "../assets/dice-1.svg";
    import dice2 from "../assets/dice-2.svg";
    import dice3 from "../assets/dice-3.svg";
    import dice4 from "../assets/dice-4.svg";
    import dice5 from "../assets/dice-5.svg";
    import dice6 from "../assets/dice-6.svg";

    export default {
        name: "Dice",
        data() {
            return {
                sides: [dice1, dice2, dice3, dice4, dice5, dice6],
                side: this.random()
            }
        },
        methods: {
            random() {
                return Math.ceil(Math.random() * 6)
            },
            diceSvg(side) {
                return this.sides[side - 1];
            },
            roll() {
                const interval = setInterval(() => {
                    this.side = this.random()

                }, 80);
                setTimeout(() => clearInterval(interval), 800)
            }
        },
        created() {
            console.log(this.side);
        }
    }
</script>
<style>
  #dice {
    height: 150px;
    display: block;
  }
</style>
