<template>
    <div>
    <input type="text" ref="fenInput" v-model="fen">
        <button @click="loadFen">go
    </button>
    </div>
</template>
<script>
import bus from "./bus.js"

export default {
    data: function(){
        return {
            fen: null
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