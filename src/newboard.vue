
<script>
import { stat } from 'fs'
import { chessboard, move } from 'vue-chessboard'
import bus from './bus.js'
import { Chess } from 'chess.js'
import AIDisplayVue from './AIDisplay.vue'

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
    evalBoard(boardstate, maximizingPlayer, moves) {
      if (boardstate.isCheckmate()) {
        console.log("checkmate max", maximizingPlayer)
        return maximizingPlayer ? -1000 : 1000
      }
      let fen = boardstate.fen()
      fen = fen.split(" ")[0]
      let pawns = (fen.split("p").length - 1) - (fen.split("P").length - 1)
      let knights = (fen.split("n").length - 1) - (fen.split("N").length - 1)
      let bishops = (fen.split("b").length - 1) - (fen.split("B").length - 1)
      let rooks = (fen.split("r").length - 1) - (fen.split("R").length - 1)
      let queens = (fen.split("q").length - 1) - (fen.split("Q").length - 1)
      let evaluation = pawns + (3 * knights) + (3 * bishops) + (5 * rooks) + (9 * queens)

      return evaluation
      //let pawns = fen.split("p").length - 1
    },
    minimax(boardstate, depth, maximizingPlayer, alpha, beta) {
      let availableMoves = boardstate.moves()
      if (depth == 0 || boardstate.isGameOver()) {
        return this.evalBoard(boardstate, maximizingPlayer, availableMoves)
      }
      if (maximizingPlayer) {
        let value = -10000
        for (let i = 0; i < availableMoves.length; i++) {
          let clonedBoard = new Chess(boardstate.fen())
          clonedBoard.move(availableMoves[i])
          let minimax = this.minimax(clonedBoard, depth - 1, false, alpha, beta)
          value = Math.max(value, minimax)
          if (value >= beta) {
            break
          }
          alpha = Math.max(alpha, value)
        }
        return value
      }
      else { //minimizing
        let value = 10000
        for (let i = 0; i < availableMoves.length; i++) {
          let clonedBoard = new Chess(boardstate.fen())
          clonedBoard.move(availableMoves[i])
          let minimax = this.minimax(clonedBoard, depth - 1, true, alpha, beta)
          value = Math.min(value, minimax)
          if (value <= alpha) {
            break
          }
          beta = Math.min(beta, value)
        }
        return value
      }
    },
    calculateBestMove(moves, fen) {
      var bestSoFar = -10000
      let moveReturn = null
      const randomizeBest = true
      let candidateMoves = []

      for (var i = 0; i < moves.length; i++) {
        let AIChess = new Chess(fen)
        AIChess.move(moves[i]);
        let evaluation = this.minimax(AIChess, 2, false, -10000, 10000)
        if (evaluation > bestSoFar) {
          bestSoFar = evaluation
          moveReturn = moves[i]
          candidateMoves = []
          candidateMoves.push(moves[i])
          //console.log(evaluation, moves[i])
        }
        else if (evaluation == bestSoFar) {
          candidateMoves.push(moves[i])
        }
      }
      if (candidateMoves.length > 1) {
        console.log("picking from ", candidateMoves.length, " candidatemoves")
        moveReturn = candidateMoves[candidateMoves.length * Math.random() | 0]
      }
      return [moveReturn, "test"]
    },
    aiNextMove() {
      let moves = this.game.moves()
      let fen = this.game.fen()
      console.log(moves)
      let [bestMove, moveInfo] = this.calculateBestMove(moves, fen)
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
