<template>
    <div>
    <input type="text" ref="fenInput" v-model="fen" placeholder="fen">
        <button @click="loadFen">go
    </button>
    </div>
</template>
<script>
import bus from "./bus.js"

export default {
    data: function(){
        return {
            fen: "1k6/prp5/2r5/7R/6R1/8/8/K7 w - - 0 1"
        }
    },
    methods: {
        loadFen(){
            console.log('hello', this.fen)
            bus.$emit('loadFen', this.fen)
        }
    }
}
</script>