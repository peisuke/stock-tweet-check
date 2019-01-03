<template>
  <div class="main">
    <b-tabs>
      <b-tab title="ウルフTweet" active>
        <stock-cards v-bind:codes="codes"/>
      </b-tab>
      <b-tab title="新高値" active>
        <stock-cards v-bind:codes="codes"/>
      </b-tab>
    </b-tabs>
  </div>
</template>

<script>
import axios from 'axios'
import StockCards from './StockCards.vue'

export default {
  name: 'Main',
  data () {
    return {
      ret: null,
      codes: null
    }
  },
  mounted: function () {
    const url = 'https://po61hlf775.execute-api.us-west-2.amazonaws.com/speculative'
    axios.get(url, {params: {'type': 'speculative'}})
      .then(ret => {
        this.ret = JSON.parse(ret.data.body)
        this.codes = this.ret.reduce(
          (obj, item) => Object.assign(
            obj, {[item.code]:
              {
                'code': item.code,
                'name': item.name,
                'date': [],
                'url': 'https://chart.yahoo.co.jp/?code=' + item.code + '.T&tm=3m&type=c&log=off&size=m&over=m25,m75&add=v,m&comp='
              }}), {})

        this.ret.forEach(item => {
          this.codes[item.code]['date'].push(item.date)
        })
      })
  },
  components: {
    StockCards
  }
}
</script>

<style lang="css" scoped>
.list-group {
  overflow: auto;
  height: 100px;
  line-height: 1.5;
  padding:0px 0;
}
</style>
