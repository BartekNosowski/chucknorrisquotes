<template>
  <div class="container bg-dark border dices">
    <div class="header">The Dice Game</div>
    <button type="button" class="btn border game-rules" data-toggle="modal" data-target="#exampleModalCenter">Game rules</button>

    <div class="text-styling">Roll types:</div>
    <span></span>
    <div class="row" style="justify-content: center">
    <button @click="dicing(false)" class="button-special-lower m-1">Lower</button>
    <button @click="dicing(true)" class="button-special-higher m-1">Higher</button>
<!--    <button @click="pointsEarned()" class="button-special-higher m-1">+</button>-->
    </div>
    <span v-if="dices" class="text-styling">{{ dices.dice[0].value }}</span>
    <img v-bind:src="img"/>
    <div class="container row">
      <div class="col-6">
      <span class="text-styling">Current round: {{whichRound}} / 30</span>
      </div>
      <div class="col-6">
        <span class="text-styling">Points earned: {{parseFloat(howManyPoints).toFixed(1)}} / 3</span>
      </div>
    </div>
  </div>
  <div class="modal fade" id="exampleModalCenter" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" role="document">
      <div class="modal-content" >
        <div class="modal-header" style="background: #006130">
          <h5 class="modal-title font-color-white" style="font-size: 30px" >GAME RULES</h5>
          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body p-1">
          <div class="text-styling-modal m-0">
      <span class="row mx-0" style="justify-content: center;">
        1. You have one dice at a time
      </span>
            <span class="row mx-0" style="justify-content: center;">
        2. You can type what will be the next dice with buttons below (lower or higher)
      </span>
            <span class="row mx-0" style="justify-content: center;">
        3. If you choose correctly you gain 0.1 point.
      </span>
            <span class="row mx-0" style="justify-content: center;">
        4. There is about 30 rounds.
      </span>
            <span class="row mx-0" style="justify-content: center;">
        5. Try to earn as much points as possible :)
      </span>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" style="font-size: 20px" data-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "Users",
  data() {
    return {
      dices: null,
      dice: "",
      value: 0,
      whichRound: 0,
      howManyPoints: 0,
      modalShow: false,

    }
  },
  methods: {

    firstDice(){
      axios.get('http://roll.diceapi.com/json/d6')
          .then(res => {
            this.dices = res.data;
            this.img = "http://roll.diceapi.com/images/poorly-drawn/d6/"+res.data.dice[0].value+".png";

          })
    },
    dicing(greather) {
      axios.get('http://roll.diceapi.com/json/d6')
          .then(res => {
            this.checkDice(res.data.dice[0].value,greather)
            this.dices = res.data;
            this.img = "http://roll.diceapi.com/images/poorly-drawn/d6/"+res.data.dice[0].value+".png";
            console.log(res.data.dice[0].value)

            this.roundChange()
          })



    },
    roundChange() {
      this.whichRound++;
      if (this.whichRound === 31){
        this.whichRound = 0
        return alert("Round 30 was last check your score.")
      }
    },
    checkDice(newValue,greather){

      if(greather){

        if (newValue > this.dices.dice[0].value){
          this.pointsEarned()
        }
      }else{
        if (newValue < this.dices.dice[0].value){
          this.pointsEarned()
        }
      }


    },
    pointsEarned() {
      this.howManyPoints+=0.1;
      console.log(this.howManyPoints)
      if (this.howManyPoints >= 3.1){
        this.howManyPoints = 0
        return alert("Maximum of points - congratulations!")
      }
    },
    saveGame(){
      localStorage.whichRound = this.whichRound;
      localStorage.howManyPoints = this.howManyPoints;
    },

    loadGame(){
      if(localStorage.whichRound != null && localStorage.howManyPoints != null){
        this.whichRound = localStorage.whichRound
        this.howManyPoints = localStorage.howManyPoints
      }

    }


  },
  created: function () {
   this.loadGame();
   this.firstDice();
  },
}
</script>

<style scoped>
</style>
