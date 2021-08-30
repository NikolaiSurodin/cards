<template>
  <div class="container">
    <p>Счетчик: {{ count }}
      <button class="btn" @click="rematch">Начать заново</button>
    </p>
    <div class="wrapper-game">

      <div class="card"
           :class="{'card-flip':card.flipped}"
           v-for="(card, idx) in cards" :key="idx">
        <card-item class="card"
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

export default {
  name: "Game",
  components: { CardItem },
  data() {
    return {
      cards: [],
      selectedCards: [],
      count: 0,
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
      if ( !card.found && !card.flipped ) {
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
              }
              this.selectedCards = []
            }, 1000 )
          }
        }
      }
    },
    rematch() {
      this.count = 0
      this.createCardList()
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
  transition: transform 1s;
  transform-style: preserve-3d;
  cursor: pointer;
  position: relative;
}

.found {
  color: #000000;
}

.card-flip {
  transform: rotateY(180deg);
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