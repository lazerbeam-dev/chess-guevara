
<script>
import { stat } from 'fs'
import { chessboard }  from 'vue-chessboard'
import bus from './bus.js'
import { Chess } from 'chess.js'

export default {
  name: 'newboard',
  extends: chessboard,
  methods: {
    undo() {
      this.game.undo()
      this.board.set({fen: this.game.fen()})
    },
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
    evalBoard(fen, moves) {
      fen = fen.split(" ")[0]
      if(moves.length == 0){
        return - 1000
      }

      let pawns = (fen.split("p").length - 1) - (fen.split("P").length -1)
      let knights = (fen.split("n").length - 1) - (fen.split("N").length -1)
      let bishops = (fen.split("b").length - 1) - (fen.split("B").length -1)
      let rooks = (fen.split("r").length - 1) - (fen.split("R").length -1)
      let queens = (fen.split("q").length - 1) - (fen.split("Q").length -1)

      let evaluation = pawns + (3 * knights) + (3 * bishops) + (5 * rooks) + (9 * queens)
      return evaluation
      //let pawns = fen.split("p").length - 1
    },
    applyMoveToFen(fen, move){

      let fenlines= fen.split("/");
      for(var i = 0; i< fenlines.length; i++){
        if(i + 1 == move[move.length -1]){
          console.log(move[move.length -1], i+1)
  
        }
      }
    },
    calculateBestMove(state, moves){
      console.log(moves)
      for(var i =0; i< moves.length; i++){
        let AIChess = new Chess(this.game.fen())
        AIChess.move(moves[i]);
        console.log(AIChess.ascii())
        //his.evalBoard(fen, )
      }
    },
    aiNextMove() {
      let moves = this.game.moves()

      this.calculateBestMove(this.game.fen(), moves)

      this.$emit('showInfo', this.evalBoard(this.game.fen(), moves))
      let randomMove = moves[Math.floor(Math.random() * moves.length)]
      this.game.move(randomMove)

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
  },
  mounted() {
    this.board.set({
      movable: { events: { after: this.userPlay()} },
    })
  },
  created() {
    bus.$on('undo', () => {
      this.undo()
    })
  }
}
</script>
