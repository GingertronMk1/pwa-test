<template>
  <div v-if="display" class="camera">
    <video id="player" :srcObject.prop="stream" autoplay />
    <div class="camera__buttons">
      <button @click="takePhoto" v-text="`Take Photo`" />
      <button @click="printInfo" v-text="`console.log info`" />
    </div>
    <canvas
      v-show="showCanvas"
      id="canvas"
      :width="canvasDimensions.width"
      :height="canvasDimensions.height"
    />
  </div>
  <div v-else>
    <img :src="imgSrc" alt="" />
    <button @click="setupPhoto">Retake</button>
  </div>
</template>
<script>
const navigator = window.navigator
export default {
  name: 'CameraPage',
  props: {},
  data() {
    const dimensions = {
      width: { min: 1024, ideal: 1280, max: 1920 },
      height: { min: 576, ideal: 720, max: 1080 },
    }
    return {
      display: false,
      stream: null,
      canvasDimensions: {
        width: dimensions.width.ideal,
        height: dimensions.height.ideal,
      },
      showCanvas: false,
      imgSrc: null,
    }
  },
  mounted() {
    this.setupPhoto()
  },
  methods: {
    async setupPhoto() {
      console.log(navigator)
      if (navigator && navigator.mediaDevices) {
        const stream = await navigator.mediaDevices.getUserMedia({
          audio: false,
          video: {
            facingMode: 'environment',
            ...this.dimensions,
          },
        })
        this.stream = stream
        this.display = true
      }
    },
    printInfo() {
      console.table(this.stream.getVideoTracks()[0].getSettings())
    },
    takePhoto() {
      const settings = this.stream.getVideoTracks()[0].getSettings()
      const { width, height } = settings
      console.table(settings)
      this.canvasDimensions = {
        width,
        height,
      }
      const player = document.getElementById('player')
      const canvas = document.querySelector('canvas#canvas')
      const context = canvas.getContext('2d')
      context.drawImage(player, 0, 0, width, height)
      const data = canvas.toDataURL('image/png')
      this.imgSrc = data
      this.stream.getVideoTracks().forEach((track) => track.stop())
      this.display = false
    },
  },
}
</script>

<style lang="scss">
video,
canvas,
img {
  width: 100%;
  max-width: 100%;
}
video {
  height: 100%;
}

.camera {
  position: relative;

  &__buttons {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    align-items: stretch;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 5rem;
    position: absolute;
    background-color: rgba(0, 0, 0, 0.8);

    & > button {
      color: white;
    }
  }
}
</style>
