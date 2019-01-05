<template>
  <div class="main">
    <loading-overlay v-if='isLoading' />
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
import LoadingOverlay from './LoadingOverlay.vue'
import StockCards from './StockCards.vue'

export default {
  name: 'Main',
  data () {
    return {
      isLoading: false,
      speclativeCodes: null,
      weeklyRankingCodes: null,
      newHighCodes: null
    }
  },
  methods: {
    point: function (text, dateStr) {
      const d = 'Rank: 54, Ratio: 11'
      const blocks = d.split(',')
      const blockRank = blocks[0].split(':')
      const rank = Number(blockRank[1])
      const rankPoint = (100 - rank) / 200 + 0.5

      const now = new Date()
      const date = new Date(dateStr)
      const old = new Date()
      old.setDate(old.getDate() - 120)
      const datePoint = 1 - (now - date) / (now - old)

      return rankPoint + datePoint
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
      result = Object.keys(result).map(function (key) { return result[key] })
      result.sort(function (lt, rt) {
        return rt.point - lt.point
      })
      result = result.slice(0, 50)
      return result
    }
  },
  mounted: function () {
    this.isLoading = true
    let promises = []
    const url = 'https://po61hlf775.execute-api.us-west-2.amazonaws.com/prod'
    promises.push(
      axios.get(url, {params: {'type': 'speculative'}})
        .then(ret => {
          this.speclativeCodes = this.parse(JSON.parse(ret.data.body), null)
        }))
    promises.push(
      axios.get(url, {params: {'type': 'new-high'}})
        .then(ret => {
          this.newHighCodes = this.parse(JSON.parse(ret.data.body), null)
        }))
    promises.push(
      axios.get(url, {params: {'type': 'weekly-ranking'}})
        .then(ret => {
          this.weeklyRankingCodes = this.parse(JSON.parse(ret.data.body), this.point)
        }))

    axios.all(promises)
      .finally(axios.spread((acct, perms) => {
        this.isLoading = false
      }))
  },
  components: {
    LoadingOverlay,
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
