<template>
  <div id="app">
    <HereMap
      apikey="0apoW9UqaYuTDfuuuLoL1auhoWXmMJkbNniDi0e3NIQ"
      lat="-34.921230"
      lng="138.599503"
      width="100%"
      height="835px" />
  </div>
</template>

<script>
    import HereMap from "./components/HereMap.vue"

    export default {
        name: 'app',
        components: {
            HereMap
        }
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
