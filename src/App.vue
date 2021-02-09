<template>
  <router-view/>
  <nav class="fixed-bottom bg-light text-center">
    <!-- TODO: replace spotify iframe with api -->
    <iframe :src="trackId" width="100%" height="80" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe>
    <small class="text-muted p-2">
      coded with ❤️ by <a clas="text-decoration-none text-black" href="https://www.buymeacoffee.com/realrecordzlab">realrecordzLab</a>
    </small>
  </nav>
</template>

<script>
import citations from './serenella'
import Chance from 'chance'

export default {
  name: 'App',
  data() {
    return {
      trackId: null      
    }
  },
  metaInfo: {
    name: 'serenella says'
  },
  mounted() {
    this.run()
  },
  methods: {
    run() {
      let cookie = document.cookie
      let chance = new Chance();
      let hash = chance.fbid();
      let n = chance.integer({min: 0, max: Object.keys(citations).length}); 
      this.trackId = `https://open.spotify.com/embed/track/${Object.keys(citations)[n]}`; 
      console.log(this.trackId)   
      if( cookie.split('=')[0] === 'uid'){
        this.$router.push({path: '/about'});
      }else {
        this.$router.push({name: 'Home', params: {userid: hash} });
      } 
    }
    //TODO: random track from api
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
