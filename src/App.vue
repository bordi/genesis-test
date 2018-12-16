<template>
  <div id="app">
    <DataGrid :columns="titles"
              :data="items"
              :rows-per-page="15" />
  </div>
</template>

<script>
import DataGrid from './components/DataGrid'
import DATA from './initialData'

export default {
  name: 'App',
  components: {
    DataGrid,
  },
  data () {
    return DATA
  },
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;

    color: #2c3e50;
  }
</style>
