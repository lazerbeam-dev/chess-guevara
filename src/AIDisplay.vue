<template>
    <div>
        {{  text }}
        <div>
        {{ time }}
    </div>
    </div>
    
</template>
<script>
import bus from './bus.js'
export default {
  name: 'AIDisplay',
  data: function(){
    return {
        text: "the revolutionary plays the game of the capitalists, so you go first, capitalist pig",
        taunts: ["I will redistribute your life!", 
        "you fail to see the bias inherent in the system", 
        "let us free ourselves together brother", 
        "why don't we promote all the pawns and have a good time?"
            ],
        checks: [
            "check your privelege",
            "check yourself before you shrek yourself, swamp face",
            "check... check... ah one two, revolution comin though"
            ],
        losses: [
            "you play well, capitalist, but revolution is inevitable!"
        ],
        wins: ["victory is mine, capitist, now let's play one to decide who gets to rule Canada"],

        time: null
    }   
  },
  methods: {
    Taunt(){
        this.text = this.taunts[Math.floor(Math.random() * this.taunts.length)]
    },
    ExplainCalculation(moveInfo){
        
    },
    GameEnd(){
        
    }
  },
  created() {
    bus.$on('AImove', (move, moveInfo) => {
        console.log("hello", move)
      this.Taunt()
    })
  }
}
</script>