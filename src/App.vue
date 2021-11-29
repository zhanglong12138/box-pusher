<!--
 * @author: Taolu
 * @Date: 2021-11-29 09:02:27
-->
<template>
    <BoxPusher />
</template>

<script>
import BoxPusher from "./components/BoxPusher";
export default {
  data(){
    return {

    }
  },
  name: 'App',
  components: {
    BoxPusher
  }
}
</script>

<style>
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

</style>
