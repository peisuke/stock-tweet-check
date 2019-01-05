<template>
  <transition name='loading'>
    <div class="loading">
      <div class="overlay">
        <div class="panel">
          <circle8 style='margin:10px auto;'></circle8>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import {Circle8} from 'vue-loading-spinner'

export default {
  components: {
    Circle8
  }
}
</script>

<style lang="css" scoped>
.loading-enter-active {
  transition: all 0.1s;
}
.loading-leave-active {
  transition: all 0.1s ease 0.1s;
}
.panel {
  width: 96px;
  height: 96px;
  padding: 20px;
  background: rgba(255, 255, 255, 0.5);
  position: absolute;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
  margin: auto;
  transition: all .3s ease;
}
.modal-enter,
.modal-leave-active {
    opacity: 0;
}
.modal-enter .panel,
.modal-leave-active .panel{
    top: -200px;
}
.overlay {
    background: rgba(0, 0, 0, .8);
    position: fixed;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    z-index: 900;
    transition: all .5s ease;
}
</style>
