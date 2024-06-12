<template>
   <div>
    <nav class="navbar navbar-expand-lg bg-body-tertiary" style="box-shadow: 0px 0.5px 2px 1px; margin-bottom: 30px;">
      <div class="container d-flex justify-content-between align-items-center">
        <a class="navbar-brand" href="#">
          <img src="./../assets/iq-logo.png" alt="Brand Logo" width="160">
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse justify-content-center" id="navbarSupportedContent">
          <ul class="navbar-nav mb-2 mb-lg-0">
       
            <li class="nav-activeprop" v-if="route == '/'" >
              <button class="nav-link active"  @click="runRouteChange('/')" >Templates</button>
              <!-- <router-link to="/" class="nav-link active" @click="runFuntion" aria-current="page">Templates</router-link> -->
            </li>
            <li class="nav-active" v-else >
              <button class="nav-link active" @click="runRouteChange('/')" >Templates</button>
              <!-- <router-link to="/" class="nav-link active" @click="1runFuntion" aria-current="page">Templates</router-link> -->
            </li>

            <li class="nav-activeprop" v-if="route == '/selecttemplate'">
              <button class="nav-link active" @click="runRouteChange('/selecttemplate')"  >Extract Data</button>
              <!-- <router-link to="/selecttemplate" @click="runFuntion" class="nav-link">Extract Data</router-link> -->
            </li>
            
            <li class="nav-item" v-else>
              <button class="nav-link active" @click="runRouteChange('/selecttemplate')"  >Extract Data</button>
              <!-- <router-link to="/selecttemplate" @click="runFuntion" class="nav-link">Extract Data</router-link> -->
            </li>
          </ul>
        </div>
        <a class="navbar-brand" href="#">
          <img src="./../assets/logo1.png" alt="Brand Logo" width="40">
        </a>
      </div>
    </nav>
  </div>


</template>

<script>
export default{
 data(){
  return{
    route:'/',
  }
 },
 wathch:{
  route(newVal){
    console.log(newVal);
  },
  $router(newVal){
    console.log(newVal);
  }
 },
 methods:{
  runRouteChange(newRoute){
    console.log(newRoute);
    if(this.route != newRoute){
      this.route = newRoute;
      this.$router.push(newRoute);
    }
  }
 },
 created(){
  this.route = this.$router.history._startLocation
  
 }

    
}
</script>


<style scoped>
.navbar {
  background-color: var(--bs-body-bg); /* Adjust based on your theme */
}

.navbar-nav {
  display: flex;
  justify-content: center;
  align-items: center;
  flex: 1; /* Take remaining space */
}

.navbar-brand {
  display: flex;
  align-items: center;
}

.navbar-nav .nav-item .nav-link {
  color: #000; /* Adjust color based on your theme */
  transition: color 0.3s;
}

.navbar-nav .nav-item .nav-link:hover {
  color: #007bff; /* Highlight color on hover */
}

.nav-activeprop{
  color: #007bff;
  background-color: rgb(158, 158, 158);
}
@media (max-width: 767px) {
  .navbar {
    background-color: var(--bs-body-bg); /* Adjust based on your theme */
  }
}
</style>
