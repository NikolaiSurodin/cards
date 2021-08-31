<template>
  <div class="container">
    <p>
      Счетчик шагов: {{ count }}

      Время:{{ secondsInMinutes }}

      <button class="btn"
              @click="startGame"
              :disabled="this.winner"
      >
        Начать игру
      </button>
    </p>
    <div class="wrapper-game">

      <div class="card"
           :class="{'card-flip':card.flipped}"
           v-for="(card, idx) in cards" :key="idx">

        <card-item class="card"
                   :value="card.value"
                   @clickOnCard="clickOnCard(card)">

          <template v-slot:front>
            <div class="front"
                 :class="{'found': card.found}"
                 v-if="!card.flipped">
              {{ card.found ? '✔' : '?' }}
            </div>

          </template>
          <template v-slot:back>
            <div class="back"
                 v-if="card.flipped">
              {{ card.value }}
            </div>
          </template>

        </card-item>
      </div>
    </div>
  </div>
</template>

<script>
import CardItem from "@/components/CardItem"
import moment from "moment"

export default {
  name: "Game",
  components: { CardItem },
  data() {
    return {
      cards: [],
      selectedCards: [],
      foundCardList: [],
      count: 0,
      startedTimer: false,
      time: 0,
      timer: null,
      winner: false,
      disabledStart: false
    }
  },
  created() {
    this.createCardList()
  },
  methods: {
    createCardList() {
      let count = 0
      let list = [ ...Array( 8 ) ].map( () => (count += 1) )
      this.cards = [ ...list, ...list ].map( value => {
        return {
          value: value,
          flipped: false,
          found: false,
          front: '?'
        }
      } )
      this.mixCardList( this.cards )
    },
    mixCardList(cardList) {
      for (let i = cardList.length - 1; i > 0; i--) {
        let j = Math.floor( Math.random() * (i + 1) );
        [ cardList[i], cardList[j] ] = [ cardList[j], cardList[i] ];
      }
    },
    clickOnCard(card) {
      if ( !card.found && !card.flipped && this.disabledStart ) {
        if ( this.selectedCards.length < 2 ) {
          card.flipped = true
          this.selectedCards.push( card )
          if ( this.selectedCards.length === 2 ) {
            this.count++
            setTimeout( () => {
              this.selectedCards.forEach( el => el.flipped = false )
              if ( this.selectedCards[0].value === card.value ) {
                this.selectedCards[0].found = true
                card.found = true
                this.foundCardList.push( this.selectedCards[0] )
                this.foundCardList.push( card )
              }
              this.selectedCards = []
              if ( this.cards.length === this.foundCardList.length ) {
                this.winnerInfo()
              }
            }, 1000 )
          }
        }
      }
    },
    winnerInfo() {
      this.stopTimer()
      this.winner = true
      this.$swal.fire( {
        title: 'Отлично! Игра завершена',
        text: `Вы потратили времени ${ this.secondsInMinutes } и шагов ${ this.count }`,
        icon: 'success',
        showCancelButton: false,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#823d3d',
        allowOutsideClick: false,
        cancelButtonText: 'Звершить',
      } )
    },
    startGame() {
      if ( !this.disabledStart ) {
        this.$swal.fire( {
          title: 'Начнем?',
          text: "Запомните цифры и найдите пары",
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#823d3d',
          allowOutsideClick: false,
          cancelButtonText: 'В другой раз',
          confirmButtonText: 'Начинаем!'
        } )
            .then( (result) => {
              if ( result.isConfirmed ) {
                this.disabledStart = true
                this.time = 0
                this.cards.forEach( el => el.flipped = true )
                setTimeout( () => {
                  this.startedTimer = true
                  this.cards.forEach( el => {
                    el.flipped = false

                  } )
                }, 5000 )
              }
            } )
      } else if ( this.disabledStart ) {
        this.stopTimer()
        this.$swal.fire( {
          title: 'Начнем сначала? Результаты будут сброшены',
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#823d3d',
          allowOutsideClick: false,
          cancelButtonText: 'Отмена',
          confirmButtonText: `Начать сначала!`
        } )
            .then( (result) => {
              if ( result.isConfirmed ) {
                this.repeatGame()
                this.cards.forEach( el => el.flipped = true )
                setTimeout( () => {
                  this.startTimer()
                  this.cards.forEach( el => {
                    el.flipped = false
                  } )
                }, 5000 )

              } else if ( result.isDismissed ) {
                this.startTimer()
              }
            } )
      }
    },
    repeatGame() {
      this.disabledStart = true
      this.time = 0
      this.timer = null
      this.count = 0
      this.foundCardList = []
      this.selectedCards = []
      this.createCardList()
    },
    startTimer() {
      this.timer = setInterval( () => {
        this.time++
      }, 1000 )
    },
    stopTimer() {
      clearInterval( this.timer )
    },
  },
  computed: {
    isDisabledStart() {
      return this.disabledStart
    },
    secondsInMinutes() {
      return moment()
          .startOf( "day" )
          .seconds( this.time )
          .format( "HH:mm:ss" );
    }
  },
  watch: {
    startedTimer() {
      this.startTimer()
    }
  }
}
</script>

<style scoped>
.wrapper-game {
  display: grid;
  justify-items: center;
  grid-template-columns: repeat(4, 200px);
  grid-gap: 10px;
  grid-auto-rows: 200px;
  justify-content: center;
}

.card {
  padding: 9px 0;
  width: 160px;
  transition: transform 0.5s;
  transform-style: preserve-3d;
  cursor: pointer;
  position: relative;
}

.found {
  color: #000000;
}

.card-flip {
  transform: rotateY(180deg);
  transition: 0.5s;
}

.back {
  transform: rotateY(180deg);
}

.container {
  display: flex;
  justify-content: center;
  flex-direction: column;
}

.container p {
  text-align: center;
}

.btn {
  font-weight: 600;
  font-size: 14px;
  padding: .5rem 1rem;
  margin: 0 6px;
  border: 2px solid #fff;
  border-radius: 5px;
  cursor: pointer;
  transition: all .25s ease;
}

.btn:hover {
  background-color: #77e68f;
}

</style>