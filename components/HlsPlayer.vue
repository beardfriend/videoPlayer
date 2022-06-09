<template>
  <div ref="videoList" class="hlsplayer-container">
    <div ref="videoContainer" class="hlsplayer-box">
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
      <div>
        <img class="profile" :src="this.profile" />
      </div>
      <div>
        <p>{{ this.title }}</p>
        <p>{{ this.channel }}</p>
      </div>
    </div>
  </div>
</template>
<script>
import Hls from 'hls.js'
import Vue from 'vue'

export default {
  name: 'HlsPlayer',
  props: ['title', 'description', 'channel', 'img', 'iframe', 'profile'],
  data() {
    return {
      isPlay: false,
      bus: new Vue(),
    }
  },

  methods: {
    ScrollTopVideoPlay() {
      if (!this.$refs.videoList) return
      if (
        this.$refs.videoList.getBoundingClientRect().top +
          this.$refs.videoList.getBoundingClientRect().height <
        150
      ) {
        this.isPlay = false
        return
      }

      if (
        this.$refs.videoList.getBoundingClientRect().top >
        this.$refs.videoList.getBoundingClientRect().height
      ) {
        this.isPlay = false
        return
      }

      if (this.$refs.videoList.getBoundingClientRect().top < 200) {
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
    },
  },

  mounted() {
    window.addEventListener('scroll', this.ScrollTopVideoPlay)
  },
  created() {},
}

// import Vue from 'vue'
</script>

<style>
.hlsplayer-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 300px;
  height: 12rem;
}

@media (width <= 638px) {
}

.profile {
  border: 2px solid red;
  border-radius: 50%;
  width: 50px;
  height: 50px;
}
.list {
  gap: 10px;
  width: 100%;
  height: 12rem;
}

.des {
  width: 100%;
  display: flex;
  gap: 10px;
}

.description {
  color: gray;
}
p {
  margin: 0;
  padding: 0;
  margin-top: 3px;
}

img {
  width: 100%;
  height: 170px;
}

video {
  background: gray;
  height: 170px;
  width: 100%;
}
</style>
