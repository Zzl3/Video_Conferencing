<template>
    <div>
      <div class="container">
          <select id="anchorType">
              <option value="摄像头">摄像头</option>
              <option value="屏幕共享">屏幕共享</option>
          </select>
          <br>
          <div class="buttonlist">
            <el-input id="userName" v-model="input1" placeholder="请输入手机号"></el-input>
            <el-button id="startConn" type="primary" size="default">连接</el-button>
            <el-input id="roomName" v-model="input2" placeholder="请输入房间号"></el-input>
            <el-button id="joinRoom" type="primary" size="default">加入房间</el-button>
            <el-button id="hangUp" type="danger" size="default">挂断</el-button>
            <el-button id="hangUp" size="default" :disabled="disabled.start" @click="start">开始录制</el-button>
            <el-button id="hangUp" size="default" :disabled="disabled.stop" @click="stop">结束录制</el-button>
            <el-button id="hangUp" size="default" :disabled="disabled.download" @click="download">下载文件</el-button>
            <div style="width:40%">
              <el-input
              id="despTion"
              v-model="input3"
              style="margin-left:2%;margin-right:2%"
              autosize
              type="textarea"
              placeholder="默认：这是一个例会"
            />
            </div>
            
          </div>
          <div id="videoContainer" class="video-container" align="center"></div>
      </div>
    </div>
</template>
<script>
export default {
  data(){
    return{
      input1:'',
      input2: '',
      input3: '这是一个例会',
      // 本地流
      stream: null,
      // 媒体录制
      mediaRecorder: null,
      // 数据块
      chunks: [],
      // 录制结果
      recording: null,
      // 按钮禁用
      disabled: {
        start: false,
        stop: true,
        download: true
      }
    }
  },
  mounted(){
    this.webrtc()
  },
  methods:{
      // 获取屏幕分享的权限
    openScreenCapture() {
      if (navigator.getDisplayMedia) {
        return navigator.getDisplayMedia({ video: true });
      } else if (navigator.mediaDevices.getDisplayMedia) {
        return navigator.mediaDevices.getDisplayMedia({ video: true });
      } else {
        return navigator.mediaDevices.getUserMedia({
          video: { mediaSource: "screen" }
        });
      }
    },
    // 开始屏幕分享录制
    async start() {
      this.disabled.start = true;
      this.disabled.stop = false;
      this.disabled.download = true;
      if (this.recording) {
        window.URL.revokeObjectURL(this.recording);
      }
      // 获取屏幕分享权限
      this.stream = await this.$options.methods.openScreenCapture();
      // 实例化一个MediaRecorder对象
      this.mediaRecorder = new MediaRecorder(this.stream, {
        mimeType: "video/webm;codecs=vp8"
      });
      // 监听可用数据
      this.mediaRecorder.addEventListener("dataavailable", event => {
        if (event.data && event.data.size > 0) {
          this.chunks.push(event.data);
        }
      });
      // 开始录制
      this.mediaRecorder.start(10);
    },
    // 停止屏幕分享录制
    stop() {
      this.disabled.start = true;
      this.disabled.stop = true;
      this.disabled.download = false;
      // 停止录制
      this.mediaRecorder.stop();
      // 释放MediaRecorder
      this.mediaRecorder = null;
      // 停止所有流式视频轨道
      this.stream.getTracks().forEach(track => track.stop());
      // 释放getDisplayMedia或getUserMedia
      this.stream = null;
      // 获取当前文件的一个内存URL
      this.recording = window.URL.createObjectURL(
        new Blob(this.chunks, { type: "video/webm" })
      );
    },
    // 下载录制的视频内容
    download() {
      console.log(this.recording);
      this.disabled.start = false;
      this.disabled.stop = true;
      this.disabled.download = true;
      let link = document.createElement('a')
      link.style.display = 'none'
      link.href = this.recording;
      link.setAttribute('download', 'load.webm')
      document.body.appendChild(link)
      link.click()
    }
  }
}
</script>
<style>
    @import '../assets/biu.css';
</style>
    

    