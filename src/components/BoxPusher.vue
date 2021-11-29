<!--
 * @author: Taolu
 * @Date: 2021-11-29 09:02:27
-->
<template>
  <div class="container">
    <div class="stage">
      <div v-show="show" class="win"></div>
      <div class="row" v-for="(item, index) in active_map" :key="index">
        <div
          :class="['block', getBlock(block)]"
          v-for="(block, rowindex) in item"
          :key="rowindex"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "BoxPusher",
  data() {
    return {
      map: [
        //0墙 1活动区域 2人物 3箱子 4终点 5箱子和终点重合
        0, 0, 0, 0, 0, 0, 
        0, 2, 1, 1, 1, 0, 
        0, 1, 3, 3, 1, 0, 
        0, 1, 1, 0, 0, 0,
        0, 1, 1, 1, 4, 0, 
        0, 1, 1, 1, 4, 0, 
        0, 0, 0, 0, 0, 0,
      ],
      static_map: [],
      active_map: [],
      stage: [6, 7],
      box_count:2,
      key2direction:{
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
      show:false
    };
  },
  beforeCreate() {
    var app = this;
    window.onkeydown = function (e) {
      app.move(e.keyCode);
    };
  },
  created() {
    if (this.map.length != this.stage[0] * this.stage[1]) {
      alert("map data error");
    }
    let map = [];
    for (let i = 0; i < this.stage[1]; i++) {
      map.push(this.map.slice(i * this.stage[0], (i + 1) * this.stage[0]));
    }
    this.static_map = deepClone(map);
    this.active_map = deepClone(map);
    this.box_count = this.calc_block(3);
  },
  mounted() {},
  computed: {
    player_position() {
      for (let y in this.active_map) {
        for (let x in this.active_map[y]) {
          if (2 === this.active_map[y][x]) return [this.static_map[y][x], +y, +x];
        }
      }
      return [0, 0];
    },
    complete_count(){
      let count = 0;
      for (let y in this.active_map) {
        for (let x in this.active_map[y]) {
          if (3 === this.active_map[y][x] && 4 === this.static_map[y][x]) count++
        }
      }
      return count;
    }
  },
  methods: {
    congratulations(){
      this.show = true;
    },
    calc_block(cate){
      let count = 0;
      for(let i in this.map){
        if(this.map[i]===cate) count++
      }
      return count;
    },
    getNextblock(direction,y,x,step=1){
      if(direction==0){
        return [this.active_map[y-step][x],y-step,x];
      }
      if(direction==1){
        return [this.active_map[y+step][x],y+step,x];
      }
      if(direction==2){
        return [this.active_map[y][x-step],y,x-step];
      }
      if(direction==3){
        return [this.active_map[y][x+step],y,x+step];
      }
    },
    move(code) {
      let index = this.key_arr.indexOf(code);
      if(index==-1) return;
      let direction = this.key2direction[""+code];
      let [orgrin,y,x] = this.player_position;
      let [nextblock,ny,nx] = this.getNextblock(direction,y,x);
      switch(nextblock){
        case 4:
        case 1:{
          this.active_map[ny][nx] = 2;
          this.active_map[y][x] = orgrin==4?4:1;
          break;
        }
        case 3:{
          let [nextblock2,by,bx] = this.getNextblock(direction,y,x,2);
          if([1,4].indexOf(nextblock2)>-1){
            this.active_map[by][bx] = 3;
            this.active_map[ny][nx] = 2;
            this.active_map[y][x] = 1;
          }
          break;
        }
      }
      if(this.complete_count==this.box_count){
        this.congratulations();
      }
    },
    getBlock(cate) {
      return ["stone", "back", "player", "box", "dream", "complete"][cate];
    },
  },
};
function deepClone(obj) {
  let newObj = Array.isArray(obj) ? [] : {};
  if (obj && typeof obj === "object") {
    for (let key in obj) {
      if (Object.prototype.hasOwnProperty.call(obj, key)) {
        newObj[key] =
          obj && typeof obj[key] === "object" ? deepClone(obj[key]) : obj[key];
      }
    }
  }
  return newObj;
}
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
</style>
