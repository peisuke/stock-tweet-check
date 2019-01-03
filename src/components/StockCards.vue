<template>
  <div class="stock-cards">
    <b-card-group deck>
      <b-card v-for="code in codes"
              v-bind:key="code.code"
              :title="code.name + ' / ' + code.code"
              tag="article"
              style="max-width: 20rem;"
              class="mb-2">
        <b-card-img :src="code.url" style="padding: 0px 0px 20px 0px;"/>
        <p class="card-text">
          <ul class="list-group">
            <li class="list-group-item list-group-item-secondary" style="height: 30px; padding: 5px 15px;"><h6>Tweet Date</h6></li>
            <li class="list-group-item" v-for="(date, index) in code.date" v-bind:key='index' style="height: 30px; padding: 5px 0px;">
              {{date}}
            </li>
          </ul>
          <a :href="'https://kabutan.jp/stock/chart?code=' + code.code" target="_blank">株ドラゴンで確認</a>
        </p>
      </b-card>
    </b-card-group>
  </div>
</template>

<script>
export default {
  name: 'StockCards',
  props: ['codes']
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
