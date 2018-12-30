<template>
  <div class="main">
    <table class="table" border="1" style='margin:0px 0px 20px 0px;'>
      <tbody>
        <tr v-for="item in ret" v-bind:key='item.date'>
          <td width='15%'>{{item.date}}</td>
          <td width='10%'>{{item.code}}</td>
          <td width='15%'>{{item.name}}</td>
          <td width='60%'><small>{{item.text}}</small></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Main',
  data () {
    return {
      ret: null
    }
  },
  mounted: function () {
    const url = 'https://xlbittrhoj.execute-api.us-west-2.amazonaws.com/default/UrufuCheck'
    axios.get(url)
      .then(ret => {
        this.ret = ret.data
      })
  }
}
</script>
