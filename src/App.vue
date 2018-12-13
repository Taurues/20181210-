<template>
  <div id="app">
    <leftnav></leftnav>
    <router-view/>
  </div>
</template>

<script>
import leftnav from '@/components/common/leftNav'
export default {
  name: 'App',
  components:{
    leftnav
  }
}
</script>

<style>
#app {
  font-family: 'Microsoft YaHei', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
