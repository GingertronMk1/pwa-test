<template>
  <div v-if="display">
    <video id="player" :srcObject.prop="stream" autoplay />
    <button @click="takePhoto">Camera</button>
    <button @click="printInfo">Info</button>
    <canvas
      v-show="showCanvas"
      id="canvas"
      :width=canvasDimensions.width
      :height="canvasDimensions.height"
    />
  </div>
  <div v-else>
    <img :src="imgSrc" alt="">
    <button @click="setupPhoto">Retake</button>
  </div>
</template>
<script>
const navigator = window.navigator;
export default {
  name: "CameraPage",
  props: {},
  data() {
    const dimensions = {
      width: { min: 1024, ideal: 1280, max: 1920 },
      height: { min: 576, ideal: 720, max: 1080 }
    }
    return {
      display: false,
      stream: null,
      canvasDimensions: {
        width: dimensions.width.ideal,
        height: dimensions.height.ideal
      },
      showCanvas: false,
      imgSrc: null
    }
  },
  mounted() {
    this.setupPhoto();
  },
  methods: {
    async setupPhoto() {
      const stream = await navigator.mediaDevices.getUserMedia({
        audio: false,
        video: {
          facingMode: 'environment',
          ...this.dimensions
        }
      })
      this.stream = stream;
      this.display = true;
    },
    printInfo() {
      console.table(this.stream.getVideoTracks()[0].getSettings());
    },
    takePhoto() {
      const settings = this.stream.getVideoTracks()[0].getSettings();
      const {width, height} = settings;
      console.table(settings);
      this.canvasDimensions = {
        width,
        height
      };
      const player = document.getElementById('player');
      const canvas = document.querySelector("canvas#canvas");
      const context = canvas.getContext('2d');
      context.drawImage(
        player,
        0,
        0,
        width,
        height
      );
      const data = canvas.toDataURL('image/png');
      this.imgSrc = data;
      this.stream.getVideoTracks().forEach(track => track.stop());
      this.display = false;
    },
  },
}
</script>

<style lang="scss">
  video, canvas, img {
    width: 100%;
    max-width: 100%;
  }
</style>
