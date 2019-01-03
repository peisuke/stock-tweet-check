<template>
  <div class="main">
    <b-card-group deck>
      <b-card v-for="code in codes"
              v-bind:key="code.code"
              :title="code.name + ' / ' + code.code"
              tag="article"
              style="max-width: 20rem;"
              class="mb-2">
        <b-card-img :src="code.url" style="padding: 10px 10px;"/>
        <p class="card-text">
          <ul class="list-group">
            <li class="list-group-item list-group-item-secondary" style="height: 30px; padding: 5px 15px;"><h6>Tweet Date</h6></li>
            <li class="list-group-item" v-for="(date, index) in code.date" v-bind:key='index' style="height: 30px; padding: 5px 15px;">
              {{date}}
            </li>
          </ul>
        </p>
      </b-card>
    </b-card-group>
  </div>
</template>

<script>
import axios from 'axios'

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
    axios.get(url)
      .then(ret => {
        this.ret = ret.data
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
