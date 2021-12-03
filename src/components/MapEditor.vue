<!--
 * @author: Taolu
 * @Date: 2021-11-29 09:02:27
-->
<template>
  <div class="container" v-cloak>
    
    <div class="menu" >
      <nav class="navbtn" @click="changestatus"  data-key="default">Return</nav>
      <nav class="navbtn" v-if="show" @click="show=false">Reset init data</nav>
      <nav class="navbtn" v-if="show" @click="save">Save & Output your map</nav>
      <nav class="navbtn" v-if="show" @click="playnow">Play it right now</nav>
    </div>
    <div class="stage">
      <div v-show="!show">
        <div class="para">
          <label for="x">row block：</label><input type="number" id="x" v-model="stage[0]">
        </div>
        <div class="para" >
          <label for="y">column block：</label><input type="number" id="y" v-model="stage[1]">
        </div>
        <div class="para">
          <label for="count">box counts：</label><input type="number" id="count" v-model="box_count">
        </div>
        <button class="initbtn" @click="init">Init my map</button>
      </div>
      
      <div class="editable" v-if="show">
        <div class="map">
          <div class="row" v-for="(item, index) in static_map" :key="index">
            <div
              :class="['block', getBlock(block), (rowindex==active_position[0] && index==active_position[1])?'active':'']"
              v-for="(block, rowindex) in item"
              :key="rowindex"
              @click="active_position = [rowindex,index]"
            ></div>
          </div>
        </div>
        <div class="tips">
          1. Clicking at the block or pressing the keybroad with "w,s,a,d" to select editable block.
          <br>
          2.Inputing numbers with the keys "0-4" to change the block function status.
          <br>
          3.The box-counts must be equal to that before setting.
        </div>
        <div class="iconlist">
          <div class="block stone"></div>
          <div class="block back"></div>
          <div class="block box"></div>
          <div class="block player"></div>
          <div class="block dream"></div>
        </div>
        <div class="iconlist">
          <div class="block ">0</div>
          <div class="block ">1</div>
          <div class="block ">2</div>
          <div class="block ">3</div>
          <div class="block ">4</div>
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
      // static_map: [
        //0stone 1area moveable spirit 3box 4flag 5completed pointed
        // [ 0, 0, 0, 0, 0, 0, ], 
        // [ 0, 2, 1, 1, 1, 0, ], 
        // [ 0, 1, 3, 3, 1, 0, ], 
        // [ 0, 1, 1, 0, 0, 0, ],
        // [ 0, 1, 1, 1, 4, 0, ], 
        // [ 0, 1, 1, 1, 4, 0, ], 
        // [ 0, 0, 0, 0, 0, 0, ],
      // ],
      static_map:[],
      stage: [6, 7],
      box_count:2,
      keys:{
        "87":1,
        "38":1,
        "83":2,
        "40":2,
        "65":3,
        "37":3,
        "68":4,
        "39":4,

        "48":0,
        "96":0,
        "97":-1,
        "49":-1,
        "98":-2,
        "50":-2,
        "99":-3,
        "51":-3,
        "100":-4,
        "52":-4,
      },
      key_arr:[87,38,83,40,65,37,68,39,49,50,51,52,53,96,97,98,99,100,48,96],
      active_position:[1,1],
      show:false,
    };
  },
  beforeCreate() {

  },
  created() {
    var app = this;
    window.onkeydown = function (e) {
      app.keypress(e.keyCode);
    };
  },
  mounted() {},
  computed: {},
  methods: {
    changestatus(e){
      this.$emit("changestatus",e);
    },
    playnow(){
      if(this.singlecheckmap()){
        this.$emit("play",{
          map:this.getstaticmap(),
          stage:[...this.stage],
        });
      }
    },
    save(){
      var singlemap = {
        map:this.getstaticmap(),
        stage:[...this.stage],
      }
      if(this.singlecheckmap()){
        var target = document.createElement('a');
        var blob = new Blob([JSON.stringify(singlemap)]);
        target.download = 'box-pusher.map.json';
        target.href = URL.createObjectURL(blob);
        target.click();
        URL.revokeObjectURL(blob);
        target = null;
      }
    },
    singlecheckmap(){
      let box_count = 0;
      let flag_count = 0;
      let static_map = this.getstaticmap();
      for(let i in static_map){
        if(static_map[i]===3){
          box_count += 1
        }
        if(static_map[i]===4){
          flag_count += 1
        }
      }
      if(box_count!==this.box_count){
        alert("Error:box_count incorrect!");
        return false;
      }
      if(flag_count!==this.box_count){
        alert("Error:flag_count incorrect!");
        return false;
      }
      if(!static_map.includes(2)){
        alert("Error:no player.Keypress 2 to set player!");
        return false;
      }
      return true;
    },
    getstaticmap(){
      return [].concat(...this.static_map);
    },
    init(){
      let [x,y] = this.stage;
      let static_map = [];
      for(let i=0; i<y; i++){
        if(i==0 || i==y-1){
          static_map.push(new Array(x).fill(0));
        }else{
          let arr = new Array(x).fill(1);
          arr[0] = 0;
          arr[x-1] = 0;
          static_map.push(arr);
        }
      }
      this.static_map = static_map;
      this.show = true;
    },
    keypress(code){
      if(!this.show) return;
      let index = this.key_arr.indexOf(code);
      if(index==-1) return;
      let fcode = this.keys[""+code];
      if(fcode=='undefined') return;
      switch(fcode){
        case 1:{
          if(this.active_position[1]>0){
            this.active_position[1]--;
          }
          break;
        }
        case 2:{
          if(this.active_position[1]<this.stage[1]-1){
            this.active_position[1]++;
          }
          break;
        }
        case 3:{
          if(this.active_position[0]>0){
            this.active_position[0]--;
          }
          break;
        }
        case 4:{
          if(this.active_position[0]<this.stage[0]-1){
            this.active_position[0]++;
          }
          break;
        }
        case  0:
        case -1:
        case -2:
        case -3:
        case -4:
        case -5:{
          this.setBlock(this.active_position[1],this.active_position[0],-fcode);
          break;
        }
        default:{

          break;
        }
      }
    },
    setBlock(y,x,cate){
      if(cate==2){
        let index = this.getstaticmap().indexOf(2);
        if(index>-1){
          for(let y2 in this.static_map){
            for(let x2 in this.static_map[y2]){
              if(this.static_map[y2][x2]===2){
                this.static_map[y2][x2] = 1;
                break;
              }
            }
          }
        }
      }
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
  border: 1px solid white;
  display: flex;
  justify-content: center;
  align-items: center;
}
@keyframes flicker{
  from{
    border:2px solid white;
  }50%{
    border:2px solid red;
  }to{
    border:2px solid white;
  }
}
.active{
  border:1px solid white;
  position:relative;
}
.active::after{
  content:" ";
  position:absolute;
  left:-2px;
  top:-2px;
  height: 40px;
  width: 40px;
  border:2px solid white;
  animation:flicker ease-in-out 2s infinite;
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
  margin:30px 0;
  width:500px;
  word-break: break-all;
  white-space: pre-line;
  line-height:2;
}

input{
  border:none;
  font-size:20px;
  text-decoration: #c9c9c9 1px solid;
  margin:10px 0;
  outline: none;
}

input:hover{
  border:none;
  /* outline: none; */
}

.initbtn{
  line-height:20px;
  padding:10px;
  margin-top:10px;
  font-size:20px;
  color:white;
  border:0;
  background: orange;
  font-weight: bold;
  margin-top:50px;
}

.iconlist{
  width:260px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.iconlist .block{
  line-height:40px;
  text-align: center;
}

</style>
