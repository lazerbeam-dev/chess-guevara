<template>
  <div id="app">
    
    <h1>Chessica </h1>
      <newboard @showInfo="x => showInfo(x)"/>
        {{ this.positionInfo }}
  </div>
</template>

<script>
import {chessboard} from 'vue-chessboard'
import 'vue-chessboard/dist/vue-chessboard.css'
import newboard from './newboard.vue'
import editor from './editor.vue'
import bus from './bus.js'

export default {
  name: 'app',
  components: {
    chessboard,
    newboard,
    editor
  },
  data () {
    return {
      currentFen: '',
      positionInfo: null
    }
  },
  methods: {
    showInfo(data) {
      this.positionInfo = data
    },
    loadFen(fen) {
      this.currentFen = fen
    },
    promote() {
      if (confirm("Want to promote to rook? Queen by default") ) {
        return 'r'
      } else {
        return 'q'
      }
    },
    undo() {
      bus.$emit('undo')
    }
  },
  created() {
  }
}
</script>
