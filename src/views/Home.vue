<template>
  <div class="container-fluid wrapper mt-5 pt-3">
    <div class="row m-0" v-show="!previewPost">
      <div class="col-sm-12 col-md-8 col-lg-8 mx-auto">
        <h2 class="hour">{{ hour }}</h2>
        <h6 class="lh-1 text-muted float-start">@serenella says</h6>
        <p class="fw-bold fs-4 mt-5 text-justify citation">{{ thing }}</p>
        <!-- subscriptions button / TODO: push notifications ? -->
        <ul class="nav">
          <li class="nav-item">
            <!-- <router-link class="nav-link ps-0" to="/about">subscribe</router-link> -->
            <a class="nav-link ps-0" href="#" @click.prevent="subscribeDevice()">subscribe</a>
          </li>
          <li class="nav-item">
            <a href="#" class="nav-link pe-0" @click.prevent="prepareToShare()">like</a>
          </li>
        </ul>
      </div>  
    </div>
    <div class="row m-0" v-show="previewPost">
      <div class="col-sm-12 col-md-7 col-lg-7 text-center mx-auto post" ref="postImage">
        <img class="img-fluid shareable-image" :src="uri">
      </div>
    </div>
  </div>
</template>

<script>
'use strict'
import citations from '@/serenella'
import moment from 'moment'
import imgCreator from 'text-to-image'
const Chance = require('chance')

export default {
  name: 'Home',
  data() {
    return {
      thing: null,
      hour: null,
      previewPost: false,
      uri: null,
    }
  },
  created() {
    this.clock();
  },
  mounted() {
    this.clock();
    this.mustRedirect().then( (redirect) => {
      if( redirect ){
        this.$router.push({path: '/about'})
      }else{
        this.initMessage()
      }
    });
  },
  methods: {
    async mustRedirect() {
      let cookie = document.cookie;
      if( cookie.split('=')[0] === 'uid' ){
        return true;
      }else{
        let hash = this.$route.params.userid
        document.cookie = `uid=${hash};max-age=86400; path=/;`
        return false;
      }
    },
    clock() {
      return setInterval( () => {
        this.hour = moment().format('LTS');
      },1000);    
    },
    initMessage() {
      const chance = new Chance();
      let rnd = chance.integer({min: 0, max: Object.keys(citations).length});
      this.thing = Object.values(citations)[rnd];
    },
    prepareToShare() {
      imgCreator.generate(this.thing).then( (uri) => {
        this.uri = uri; 
        this.previewPost = true;
        const a = document.createElement('a');
        a.setAttribute('download', `${this.$route.params.userid}`);
        fetch(uri).then( (res) => res.blob() )
        .then( (blob) => {
          let u = URL.createObjectURL(blob);
          a.href = u;
          this.$refs.postImage.appendChild(a)
          a.click();
          this.$refs.postImage.removeChild(a);
          URL.revokeObjectURL(u);
        })
        setTimeout( () => {
          this.previewPost = false;
          this.$router.push({path: '/about'})
        },5000);
      });
    },
    subscribeDevice() {
      console.log(Notification.permission);

      if( Notification.permission === 'denied' || Notification.permission === 'default' ){
        Notification.requestPermission().then( (result) => {
          console.log(result)
                    
        })
      }else{
        const message = new Notification('Serenella says!',{body: 'Subscribed!'})
      }

    }
  }
}
</script>

<style lang="scss" scoped>
.wrapper {
  .citation {
    background: linear-gradient(pink, palevioletred);
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  a {
    color: black;
    text-decoration: none;
    &:hover {
      color: palevioletred;
    }
  }
}

</style>
