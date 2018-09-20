# example of use

```html
<!-- your main component, see v-if connected-->
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div v-if="connected">
      <HelloWorld msg="Welcome to Your Vue.js App"/>
    </div>
    <div v-else>
      <span>connecting...</span>
  </div>
  </div>
</template>

<script>
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'home',
  components: {
    HelloWorld
  },
  computed: {
    connected(){
      return this.$store.state.sdp.isConnected
    }
  }
}
</script>

<!-- your component -->
<template>
  <div v-if="$subsReady">
    <h1>{{ msg }}</h1>
    <div class="pointer" @click="inc(c.id, c.x+1)" v-bind:key="c.id" v-for="c in myCounters">
      {{ c.x }}
    </div>  
    <button @click="suma">2 + 3 = </button>
    <span>{{valor}}</span> 
  </div>
  <div v-else>
    Loading...
  </div>
</template>

<script>
import { SDP_Mixin } from 'msdp'

export default {
  name: 'HelloWorld',
  mixins: [SDP_Mixin],
  data(){
    return {
      valor: 0,
      max: 5
    }
  },
  props: {
    msg: String
  },
  methods: {
    async suma(){
      this.valor = await this.$rpc('add', {a: 2, b: 3})
    },
    async inc(id, value){
      this.$rpc('increment', {id, value})
    }
  },
  created(){
    this.$sub('x_less_than', {max: this.max})
  },
  computed: {
    maxChange(){
      return this.max
    },
    myCounters(){
      return this.$store.state.sdp.subs.x_less_than
    }
  },
  watch: {
    maxChange(max){
      this.$sub('x_less_than', {max})
    }
  }
} 
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.pointer{
  cursor: pointer;
}
</style>
```

```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import { moduleSocket } from 'msdp'

Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    sdp: moduleSocket,
  }
})
```