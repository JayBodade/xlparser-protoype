<template>
  <div id="app">
    <NavbarComponent/>
    <!-- <nav>
      <router-link to="/">Templates</router-link> |
      <router-link to="/addtemplate">Add Template</router-link> |
      <router-link to="/selecttemplate">Extract Data From Template</router-link>
     
    </nav> -->
     
    <router-view/>
  </div>
</template>
<script>
import NavbarComponent from './components/NavbarComponent.vue';
export default{
  components:{
    NavbarComponent
  }
  
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
