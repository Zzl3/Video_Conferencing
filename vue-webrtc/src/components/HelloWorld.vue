<template>
  <div>
    <el-container>
      <el-header>
        <HeaghtListVue/>
      </el-header>
      <el-main>
        <webrtcdemo/>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import webrtcdemo from './WebRtcdemo.vue'
import HeaghtListVue from './HeaghtList.vue'
export default {
  data(){
    return{
      
    }
  },
  components:{
    webrtcdemo,
    HeaghtListVue
  },
  mounted(){
    this.webrtc()
  },
  methods:{
  }
}
</script>
<style>
  .el-header {
    background: beige;
    color: #333;
    text-align: center;
    height: 12vh !important;
  }
  .el-main {
    background: rgb(255, 255, 255);
    color: #333;
    height: 85vh;
  }
</style>
