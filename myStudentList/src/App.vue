<template>
  <div id="app">
    <div id="nav">
     <NavTop/>
     <NavLeft/>
    
    </div>
    <router-view/>
  </div>
</template>
<script>


import NavTop from './views/nav'
import NavLeft from './views/navleft'
export default {
  
  components: { 
    NavTop,
    NavLeft,
    
    
    },
  
}
</script>

<style lang="scss">
*{
  margin: 0;
  padding: 0;
}
body,html{
  height: 100%;
}
#app {
  height: 100%;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  height: 100%;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
