
<script>
import { stat } from 'fs'
import { chessboard, move } from 'vue-chessboard'
import bus from './bus.js'
import { Chess } from 'chess.js'
import AIDisplayVue from './AIDisplay.vue'
import * as AI from './AILogic'

export default {
  name: 'newboard',
  extends: chessboard,
  components: {
    AIDisplayVue
  },
  data: function () {
    return {
      previousMoves: null,
    }
  },
  methods: {
    userPlay() {
      return (orig, dest) => {
        if (this.isPromotion(orig, dest)) {
          this.promoteTo = this.onPromotion()
        }
        this.game.move({from: orig, to: dest, promotion: this.promoteTo}) // promote to queen for simplicity
        this.board.set({
          fen: this.game.fen()
        })
        this.calculatePromotions()
        this.aiNextMove()
      };
    },
    aiNextMove() {
      let moves = this.game.moves()

      bus.$emit('AIHasMoves', moves)
      let fen = this.game.fen()
      console.log(moves)
      let [bestMove, moveInfo] = AI.calculateBestMove(moves, fen)
      console.log(bus)
      bus.$emit('AImove', [bestMove, moveInfo])
      this.game.move(bestMove)
      console.log(this)
      console.log(this.toColor())

      this.board.set({
        fen: this.game.fen(),
        turnColor: this.toColor(),
        movable: {
          color: this.toColor(),
          dests: this.possibleMoves(),
          events: { after: this.userPlay()},
        }
      });
    },
    undo() {
      console.log('trying to undo');
    },
    loadFen(newFen) {
      console.log('woo', newFen)
      //var f = this.validateFen(newFen)
      //console.log(f);
      console.log(this.game)
      this.game.load(newFen)
      this.board.set({
        fen: this.game.fen(),
        turnColor: "white",
        movable: {
          color: this.toColor(),
          dests: this.possibleMoves(),
          events: { after: this.userPlay()},
        }
      });
    }
  },
  mounted() {
    this.board.set({
      movable: { events: { after: this.userPlay() } },
    })
  },
  created() {
    bus.$on('undo', () => {
      this.undo()
    })
    bus.$on('loadFen', (newFen) => {
      console.log('yay')
      this.loadFen(newFen)
    })
  },

}
</script>
