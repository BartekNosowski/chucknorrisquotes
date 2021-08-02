<template>
  <div class="container bg-dark border border-radius">
    <div class="header">The Dice Game</div>
    <button type="button" class="btn border game-rules" data-toggle="modal" data-target="#exampleModalCenter">Game
      rules
    </button>
    <br>
    <button @click="firstDice()" class="btn border start-game bg-info row m-1">Start game</button>
    <div class="text-styling">Roll types:</div>
    <span></span>
    <div class="row" style="justify-content: center">
      <button @click="dicing(false)" class="btn button-special-lower p-0 m-1">Lower</button>
      <button @click="dicing(true)" class="btn button-special-higher p-0 m-1">Higher</button>
    </div>
    <!--    <span v-if="dices" class="text-styling">{{ dices.dice[0].value }}</span>-->
    <img v-if="dices" v-bind:src="img"/>
    <div class="container row">
      <div class="col-6">
        <span class="text-styling">Current round: {{ whichRound }} / 30</span>
      </div>
      <div class="col-6">
        <span class="text-styling">Points earned: {{ parseFloat(howManyPoints).toFixed(1) }} / 3</span>
      </div>
    </div>
    <div class="row text-white mx-0 px-0 my-3 start-game" style="justify-content: center">Games History</div>
    <div>
      <table class="table text-white">
        <thead>
        <tr>
          <th scope="col">Game ID</th>
          <th scope="col">Points Earned</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="item in gamesHistory" v-bind:key="item.gameId">
          <td>{{ item.gameId }}</td>
          <td>{{ item.pointsAtGameEnd }}</td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>

  <div class="modal fade" id="exampleModalCenter" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle"
       aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" role="document">
      <div class="modal-content">
        <div class="modal-header" style="background: #006130">
          <h5 class="modal-title font-color-white" style="font-size: 30px">GAME RULES</h5>
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
  <div id="myModal" class="modal fade" role="dialog">
    <div class="modal-dialog">
      <!-- Modal content-->
      <div class="modal-content">
        <div class="modal-header bg-info">
          <h4 class="modal-title">Reload previous game?</h4>
          <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>
        <div class="modal-footer" style="justify-content: center">
          <button @click="firstDice()" type="button" class="btn btn-secondary" data-dismiss="modal">NO</button>
          <button @click="loadGame()" type="button" class="btn btn-primary" data-dismiss="modal">YES</button>
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
      whichRound: 1,
      howManyPoints: 0,
      modalShow: false,
      gameResult: {
        gameId: 0,
        pointsAtGameEnd: 0,
      },
      retrivedGameResult: {},
      retrivedGameHistory: [],
      gamesHistory: [],

    }
  },
  methods: {
    firstDice() {
      axios.get('http://roll.diceapi.com/json/d6')
          .then(res => {
            this.dices = res.data;
            this.img = "http://roll.diceapi.com/images/poorly-drawn/d6/" + res.data.dice[0].value + ".png";
            this.resetRoundPoints();
          })
    },
    resetRoundPoints() {
      this.whichRound = 1;
      this.howManyPoints = 0;
    },
    dicing(checkGreater) {
      axios.get('http://roll.diceapi.com/json/d6')
          .then(res => {
            this.checkDice(res.data.dice[0].value, checkGreater)
            this.dices = res.data;
            this.img = "http://roll.diceapi.com/images/poorly-drawn/d6/" + res.data.dice[0].value + ".png";
            this.finishedGames();
            this.roundChange()
            this.saveGame();
          })
    },
    roundChange() {
      this.whichRound++;
      if (this.whichRound === 31) {
        this.whichRound = 1
        this.howManyPoints = 0
        return alert("Round 30 was last check your score below. After clicking 'OK' scores will reset")
      }
    },
    checkDice(newValue, checkGreater) {
      if (checkGreater) {
        if (newValue > this.dices.dice[0].value) {
          this.pointsEarned()
        }
      } else {
        if (newValue < this.dices.dice[0].value) {
          this.pointsEarned()
        }
      }
    },
    pointsEarned() {
      if (this.whichRound <= 30) {
        this.howManyPoints += 0.1;
      }
      this.howManyPoints = parseFloat(parseFloat(this.howManyPoints).toFixed(2));
      if (this.howManyPoints >= 3.1) {
        this.howManyPoints = 0
        return alert("Maximum of points - congratulations!")
      }
    },
    finishedGames() {
      if (this.whichRound === 30) {
        this.gameResult.gameId++;
        this.gameResult.pointsAtGameEnd = this.howManyPoints;
        this.gamesHistory.push({...this.gameResult})
      }
    },
    saveGame() {
      localStorage.whichRound = this.whichRound;
      localStorage.howManyPoints = (this.howManyPoints).toString();
      localStorage.setItem('gameResult', JSON.stringify(this.gameResult))
      localStorage.gamesHistory = JSON.stringify(this.gamesHistory)
    },
    loadGame() {
      if (localStorage.whichRound != null && localStorage.howManyPoints != null) {
        this.whichRound = localStorage.whichRound
        this.howManyPoints = parseFloat(localStorage.howManyPoints)
      }
      this.retrivedGameResult = JSON.parse(localStorage.getItem('gameResult'))
      this.retrivedGameHistory = JSON.parse(localStorage.getItem('gamesHistory'))
    }
  },
  created: function () {
    this.loadGame();
    this.firstDice();
    this.finishedGames();
    this.saveGame();
  },
  mounted() {
    if (localStorage.whichRound) {
      this.whichRound = localStorage.whichRound
    }
    if (localStorage.howManyPoints) {
      this.howManyPoints = localStorage.howManyPoints
    }
  }
}
</script>

<style scoped>
</style>
