<template>
  <div id="app">
    <!-- Aquí se mostrará la transmisión de mi webcam -->
    <div id="video-container">
      <video ref="videoElement" autoplay></video>
    </div>

    <button @click="startWebcam">Mostrar webcam</button>
    <button @click="capturePhoto" :disabled="!videoStream">Tomar foto</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      videoStream: null
    }
  },
  methods: {
    async startWebcam() {
      try {
        this.videoStream = await navigator.mediaDevices.getUserMedia({ video: true });
        this.$refs.videoElement.srcObject = this.videoStream;
      } catch (error) {
        console.error('Error al acceder a la cámara:', error);
      }
    },
    capturePhoto() {
      const canvas = document.createElement('canvas');
      canvas.width = this.$refs.videoElement.videoWidth;
      canvas.height = this.$refs.videoElement.videoHeight;
      const context = canvas.getContext('2d');
      context.drawImage(this.$refs.videoElement, 0, 0, canvas.width, canvas.height);

      canvas.toBlob(blob => {
        if (blob) {
          const url = URL.createObjectURL(blob);

          const link = document.createElement('a');
          link.href = url;
          link.download = 'captured_photo.png';
          link.click();

          URL.revokeObjectURL(url);
        }
      }, 'image/png');
    }
  },
  beforeDestroy() {
    if (this.videoStream) {
      this.videoStream.getTracks().forEach(track => track.stop());
    }
  }
}
</script>

<style>
#video-container {
  margin: 20px;
}
</style>
