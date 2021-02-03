<template>
  <div id="app">
    <div class="wrapper">
      <SideBar />
      <Profiles />
    </div>
  </div>
</template>

<script>
import Profiles from './components/Profiles.vue';
import SideBar from './components/SideBar.vue';


export default {
  name: 'App',
  components: {
    Profiles,
    SideBar
  }
}
</script>

<style>
@import 'https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css';
/* @import './assets/css/bootstrap.min.css'; */
@import './assets/css/light-bootstrap-dashboard.css?v=2.0.0';
@import './assets/css/demo.css';

</style>
