<template>
  <div class="container-fluid mt-5 p-0">
    <div class="row m-0">
      <div class="col-sm-12 col-md-12 col-lg-12 p-0 text-center">
        <p class="fs-4 fw-bold lh-1">Next thing {{ countdown }}</p>
        <small class="">{{ date }}</small>
        <p class="fs-6">Come back later!</p>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'

export default {
  name: 'About',
  data() {
    return {
      countdown: null,
      days: null,
    }
  },
  computed: {
    date() {
      return moment().add(this.days, 'day').calendar();
    }
  },
  mounted() {
    this.initCountdown()
  },
  methods: {
    async initCountdown() {
      this.countdown = moment().endOf('week').fromNow();
      let s = this.countdown.split(' ')
      let d = s.filter( (w, i) => i === 1);
      this.days = Number(d.join(''));
    }
  }
}
</script>

<style lang="scss" scoped>

a {
  text-decoration: none;
  color: black;
}
</style>>
