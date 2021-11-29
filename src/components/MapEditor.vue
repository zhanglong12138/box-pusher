<!--
 * @author: Taolu
 * @Date: 2021-11-29 09:02:27
-->
<template>
  <div class="container">
    <div class="stage">
      <div v-if="!show">
        <div class="para">
          <lebal for="x">row block：</lebal><input id="x" type="text" v-model="stage[0]">
        </div>
        <div class="para">
          <lebal for="y">column block：</lebal><input id="y" type="text" v-model="stage[1]">
        </div>
        <div class="para">
          <lebal for="count">box counts：</lebal><input id="count" type="text" v-model="box_count">
        </div>
      <button @click="show = true">init</button>
      </div>
      <div class="editable" v-if="show">
        <div class="map">
          <div class="row" v-for="(item, index) in static_map" :key="index">
            <div
              :class="['block', getBlock(block)]"
              v-for="(block, rowindex) in item"
              :key="rowindex"
              @click="active_position = [index,rowindex]"
            ></div>
          </div>
        </div>
        <div class="tips">
          1. Clicking at the block or pressing the keybroad with "w,s,a,d" to select editable block.
          <br>
          2.Pressing the keybroad with from "1-5" to change the block function status.
          <br>
          3.The box-counts must be equal to that before setting.
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "MapEditor",
  data() {
    return {
      static_map: [
        //0stone 1area moveable spirit 3box 4flag 5completed pointed
        [ 0, 0, 0, 0, 0, 0, ], 
        [ 0, 2, 1, 1, 1, 0, ], 
        [ 0, 1, 3, 3, 1, 0, ], 
        [ 0, 1, 1, 0, 0, 0, ],
        [ 0, 1, 1, 1, 4, 0, ], 
        [ 0, 1, 1, 1, 4, 0, ], 
        [ 0, 0, 0, 0, 0, 0, ],
      ],
      stage: [6, 7],
      box_count:2,
      keys:{
        "87":0,
        "38":0,
        "83":1,
        "40":1,
        "65":2,
        "37":2,
        "68":3,
        "39":3,
      },
      key_arr:[87,38,83,40,65,37,68,39],
      active_position:[0,0],
      show:false,
    };
  },
  beforeCreate() {
    var app = this;
    window.onkeydown = function (e) {
      app.setblock(e.keyCode);
    };
  },
  created() {
    
  },
  mounted() {},
  computed: {
    
  },
  methods: {
    init(){

    },
    getNextblock(direction,y,x,step=1){
      if(direction==0){
        return [y-step,x];
      }
      if(direction==1){
        return [y+step,x];
      }
      if(direction==2){
        return [y,x-step];
      }
      if(direction==3){
        return [y,x+step];
      }
    },
    move(code) {
      console.log( code )
      let index = this.key_arr.indexOf(code);
      if(index==-1) return;
      let direction = this.keys[""+code];
      console.log(direction)
    },
    setBlock(y,x,cate){
      this.static_map[y][x] = cate;
    },
    getBlock(cate) {
      return ["stone", "back", "player", "box", "dream", "complete"][cate];
    },
  },
};
</script>
<style scoped>
.container {
  height: 100%;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.stage {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position:relative;
}
.row {
  display: flex;
  flex-direction: row;
  align-items: center;
}
.block {
  height: 40px;
  width: 40px;
  margin: 1px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.stone {
  background: #c9c9c9;
  background: url("../assets/stone.png") no-repeat;
  background-size: contain;
}
.back {
  background: url("../assets/back.png") no-repeat;
  background-size: contain;
}
.player {
  background: url("../assets/pika.png") no-repeat;
  background-size: contain;
}
.box {
  background: url("../assets/box.png") no-repeat;
  background-size: contain;
}
.dream {
  background: url("../assets/flag.png") no-repeat;
  background-size: contain;
}
.complete {
  background: orange;
  background: url("../assets/box.png") no-repeat;
  background-size: contain;
}
.win{
  height:100%;
  width:100%;
  position:absolute;
  left:0;
  top:0;
  background: url("../assets/win.png") no-repeat;
  background-size: contain;
  animation: trans 1500ms ease-in 1;
}

@keyframes trans{
  from{
    transform: rotate(0deg);
  }half{
    transform: rotate(180deg);
  }to{
    transform: rotate(360deg);
  }
}

.tips{
  width:800px;
  word-break: break-all;
  white-space: pre-line;
}

</style>
