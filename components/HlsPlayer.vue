<template>
  <div ref="videoList" class="list">
    <div ref="videoContainer" class="video">
      <video
        ref="video"
        :src="this.iframe"
        autoplay
        muted
        v-if="isPlay"
      ></video>

      <img :src="this.img" v-else />
    </div>
    <div class="des">
      <p>{{ this.title }}</p>
      <p>{{ this.channel }}</p>
      <p class="description">{{ this.description }}</p>
    </div>
  </div>
</template>
<script>
import Hls from 'hls.js'

export default {
  name: 'HlsPlayer',
  props: ['title', 'description', 'channel', 'img', 'iframe'],
  data() {
    return {
      isPlay: false,
      isLoading: false,
    }
  },
  methods: {
    ScrollTopVideoPlay() {
      if (!this.$refs.videoList) return

      if (this.$refs.videoList.getBoundingClientRect().top < 0) {
        this.isPlay = true
        if (this.$refs.video !== undefined) {
          if (this.$refs.video.src.includes('blob')) {
            return
          }
        }

        if (Hls.isSupported()) {
          const hls = new Hls()

          hls.loadSource(this.iframe)

          hls.attachMedia(this.$refs.video)
        }
      }
      if (
        this.$refs.videoList.getBoundingClientRect().top +
          this.$refs.videoList.getBoundingClientRect().height <
        0
      ) {
        console.log('get')
        this.isPlay = false
      }
    },
  },

  mounted() {
    window.addEventListener('scroll', this.ScrollTopVideoPlay)
  },
}

console.log(window.getBo)
// import Vue from 'vue'
</script>

<style>
.list {
  display: flex;
  gap: 10px;
  width: 100%;
  height: 12rem;
  margin-top: 20px;
}

.description {
  color: gray;
}
p:not(:first-child) {
  margin-top: 10px;
}

img {
  max-width: 300px;
  height: 170px;
}
video {
  background: gray;
  max-width: 300px;
  min-width: 300px;
  height: 170px;
  width: 100%;
}
</style>
