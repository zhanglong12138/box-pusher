<!--
 * @author: Taolu
 * @Date: 2021-11-29 09:02:27
-->
<template>
    <div class="container" v-cloak>
      <BoxPusher v-if="status=='default'" ref="stage" @changestatus="changestatus" :fmap="map" :fstage="stage" />
      <MapEditor v-if="status=='edit'" @changestatus="changestatus" @play="play" />
    </div>
</template>

<script>
import BoxPusher from "./components/BoxPusher";
import MapEditor from "./components/MapEditor";
var defaultmap = {
  map:[
        //0墙 1活动区域 2人物 3箱子 4终点 5箱子和终点重合
        0, 0, 0, 0, 0, 0, 
        0, 2, 1, 1, 1, 0, 
        0, 1, 3, 3, 1, 0, 
        0, 1, 1, 0, 0, 0,
        0, 1, 1, 1, 4, 0, 
        0, 1, 1, 1, 4, 0, 
        0, 0, 0, 0, 0, 0,
      ],
  stage:[6,7],
}
export default {
  data(){
    return {
      status:'default',
    }
  },
  mixins:[defaultmap],
  name: 'App',
  components: {
    BoxPusher,
    MapEditor
  },
  methods:{
    changestatus(e){
      this.status = e.currentTarget.dataset.key;
    },
    play(map){
      this.map = map.map;
      this.stage = map.stage;
      this.status = 'default';
    },
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
  margin-right:100px;
}

.menu .navbtn{
  line-height:20px;
  padding:10px;
  margin-top:10px;
  font-size:20px;
  color:white;
  border:0;
  background: orange;
  font-weight: bold;
  cursor:pointer;
}
</style>
