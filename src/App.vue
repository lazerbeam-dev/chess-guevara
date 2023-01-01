<template>
  <div id="app">
    
    <h1><img class="icon" src="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fwww.craftsmanspace.com%2Fsites%2Fdefault%2Ffiles%2Ffree-patterns%2FErnesto_Che_Guevara.jpg&f=1&nofb=1&ipt=dfe79c7f156c5dd1dd75fc3359c239a9f0ed0fd3a658d92815a34af9a99ee275&ipo=images"> Chess Guevara</h1> 
    <AIDisplayVue></AIDisplayVue>  
    <newboard @showInfo="x => showInfo(x)" ref="board" id="board"/>
        {{ this.positionInfo }}
    <InputsVue></InputsVue>
  </div>
</template>

<script>
import InputsVue from './Inputs.vue'
import 'vue-chessboard/dist/vue-chessboard.css'
import newboard from './newboard.vue'
import bus from './bus.js'
import AIDisplayVue from './AIDisplay.vue'

export default {
  name: 'app',
  components: {
    newboard,
    AIDisplayVue,
    InputsVue
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
      bus.$emit()
    }
  }
}
</script>
<style>
.icon{
  width: 60px;
  height: 60px;
}
#board{
  padding: 28px;
}

</style>
