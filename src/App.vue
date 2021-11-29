<!--
 * @author: Taolu
 * @Date: 2021-11-29 09:02:27
-->
<template>
    <div class="container" v-cloak>
      <div class="menu" v-if="status=='default'">
        <nav class="navbtn" @click="changestatus" data-key="default">Play</nav>
        <nav class="navbtn" @click="changestatus" data-key="edit">Edit map by yourself</nav>
        <nav class="navbtn" @click="importmap">Import maps</nav>
        <nav class="navbtn" @click="share">Share Box-pusher!</nav>
      </div>
      
      <div class="menu" v-if="status=='edit'">
        <nav class="navbtn" @click="changestatus"  data-key="default">Return</nav>
        <nav class="navbtn" @click="save">Save & Output your map</nav>
        <nav class="navbtn" @click="playnow">Play it right now</nav>
      </div>
      <BoxPusher v-if="status=='default'" />
      <MapEditor v-if="status=='edit'" />
    </div>
</template>

<script>
import BoxPusher from "./components/BoxPusher";
import MapEditor from "./components/MapEditor";
export default {
  data(){
    return {
      status:'edit',
    }
  },
  name: 'App',
  components: {
    BoxPusher,
    MapEditor
  },
  methods:{
    changestatus(e){
      this.status = e.currentTarget.dataset.key;
    },
    importmap(){

    },
    share(){

    },
    save(){

    },
    playnow(){

    }
  }
}
</script>

<style>
[v-cloak]{
  display:none;
}
html,body{
  height:100%;
  width:100%;
  margin:0;
}
#app{
  height:100%;
  width:100%;
  display:flex;
  align-items: center;
  justify-content: center;
}

.container{
  display: flex;
  flex-direction: row;
  align-items: center;
}

.menu{
  font-size: 30px;
  font-weight: bold;
  color:orange;
  display:flex;
  flex-direction: column;
}

.menu .navbtn{
  margin:0 20px 40px;
  cursor:pointer;
}
</style>
