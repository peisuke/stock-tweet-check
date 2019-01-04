<template>
  <div class="main">
    <b-tabs>
      <b-tab title="ウルフTweet" active>
        <stock-cards v-bind:codes="speclativeCodes"/>
      </b-tab>
      <b-tab title="新高値">
        <stock-cards v-bind:codes="newHighCodes"/>
      </b-tab>
      <b-tab title="週間値上がり率">
        <stock-cards v-bind:codes="weeklyRankingCodes"/>
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
      weeklyRankingCodes: null,
      newHighCodes: null
    }
  },
  methods: {
    point: function (text, date_str) {
      const d = 'Rank: 54, Ratio: 11'
      const blocks = d.split(',')
      const block_rank = blocks[0].split(':')
      const rank = Number(block_rank[1])
      const rank_point = (100 - rank) / 200 + 0.5
      
      const now = new Date()
      const date = new Date(date_str)
      const old = new Date()
      old.setDate(old.getDate() - 120)
      const date_point = 1 - (now - date) / (now - old)
      
      return rank_point + date_point
    },
    parse: function (data, point) {
      var result = null
      result = data.reduce(
        (obj, item) => Object.assign(
          obj, {[item.code]:
            {
              'code': item.code,
              'name': item.name,
              'date': [],
              'point': 0.0,
              'url': 'https://chart.yahoo.co.jp/?code=' + item.code + '.T&tm=3m&type=c&log=off&size=m&over=m25,m75&add=v,m&comp='
            }}), {})
      data.forEach(item => {
        result[item.code]['date'].push(item.date)
        if (point) {
          result[item.code]['point'] += point(item.text, item.date)
        } else {
          result[item.code]['point'] += 1.0
        }
      })
      result = Object.keys(result).map(function (key) {return result[key]})
      result.sort(function (lt, rt) {
        return rt.point - lt.point
      })
      result = result.slice(0, 50)
      return result
    }
  },
  mounted: function () {
    const url = 'https://po61hlf775.execute-api.us-west-2.amazonaws.com/prod'
    axios.get(url, {params: {'type': 'speculative'}})
      .then(ret => {
        this.speclativeCodes = this.parse(JSON.parse(ret.data.body), null)
      })
    axios.get(url, {params: {'type': 'new-high'}})
      .then(ret => {
        this.newHighCodes = this.parse(JSON.parse(ret.data.body), null)
      })
    axios.get(url, {params: {'type': 'weekly-ranking'}})
      .then(ret => {
        this.weeklyRankingCodes = this.parse(JSON.parse(ret.data.body), this.point)
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
