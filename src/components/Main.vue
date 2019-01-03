<template>
  <div class="main">
    <b-tabs>
      <b-tab title="ウルフTweet" active>
        <stock-cards v-bind:codes="speclativeCodes"/>
      </b-tab>
      <b-tab title="新高値">
        <stock-cards v-bind:codes="newHighCodes"/>
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
      speclativeCodes: null,
      newHighCodes: null
    }
  },
  methods: {
    parse: function (data) {
      var result = null
      result = data.reduce(
        (obj, item) => Object.assign(
          obj, {[item.code]:
            {
              'code': item.code,
              'name': item.name,
              'date': [],
              'url': 'https://chart.yahoo.co.jp/?code=' + item.code + '.T&tm=3m&type=c&log=off&size=m&over=m25,m75&add=v,m&comp='
            }}), {})
      data.forEach(item => {
        result[item.code]['date'].push(item.date)
      })
      return result
    }
  },
  mounted: function () {
    const url = 'https://po61hlf775.execute-api.us-west-2.amazonaws.com/prod'
    axios.get(url, {params: {'type': 'speculative'}})
      .then(ret => {
        this.speclativeCodes = this.parse(JSON.parse(ret.data.body))
      })
    axios.get(url, {params: {'type': 'new-high'}})
      .then(ret => {
        this.newHighCodes = this.parse(JSON.parse(ret.data.body))
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
