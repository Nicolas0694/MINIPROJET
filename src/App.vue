<template>
  <div id="app">
    <router-link to="/">[Home]</router-link>
    <router-link to="/restaurants">[Restaurants]</router-link>
    
     <router-view></router-view>
  </div>

</template>

<script>

export default {
  name: "app",
  components: {
  },
  data: function() {
    return {
      nom: "buffa"
    };
  },
  mounted()  {
    console.log("avant affichage");
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
