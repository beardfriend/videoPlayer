<template>
  <div
    class="video-container"
    v-bind:class="[
      { captions: isHidden },
      { paused: !isPlaying },
      { theater: screenMode === 'theater' },
      { scrubbing: isScrubbing },
    ]"
    data-volume-level="high"
  >
    <img ref="thumbnailImg" class="thumbnail-img" />
    <div class="video-controls-container">
      <div ref="timeline" class="timeline-container">
        <div class="timeline">
          <img ref="previewImg" class="preview-img" />
          <div class="thumb-indicator"></div>
        </div>
      </div>
      <div class="controls">
        <button class="play-pause-btn" @click="handleVideoPlay">
          <svg class="play-icon" viewBox="0 0 24 24">
            <path fill="currentColor" d="M8,5.14V19.14L19,12.14L8,5.14Z" />
          </svg>
          <svg class="pause-icon" viewBox="0 0 24 24">
            <path fill="currentColor" d="M14,19H18V5H14M6,19H10V5H6V19Z" />
          </svg>
        </button>
        <div class="volume-container">
          <button class="mute-btn" @click="handleVolumeMute">
            <svg
              v-bind:class="{ show: volumeLevel === 'high' }"
              class="volume-high-icon"
              viewBox="0 0 24 24"
            >
              <path
                fill="currentColor"
                d="M14,3.23V5.29C16.89,6.15 19,8.83 19,12C19,15.17 16.89,17.84 14,18.7V20.77C18,19.86 21,16.28 21,12C21,7.72 18,4.14 14,3.23M16.5,12C16.5,10.23 15.5,8.71 14,7.97V16C15.5,15.29 16.5,13.76 16.5,12M3,9V15H7L12,20V4L7,9H3Z"
              />
            </svg>
            <svg
              v-bind:class="{ show: volumeLevel === 'low' }"
              class="volume-low-icon"
              viewBox="0 0 24 24"
            >
              <path
                fill="currentColor"
                d="M5,9V15H9L14,20V4L9,9M18.5,12C18.5,10.23 17.5,8.71 16,7.97V16C17.5,15.29 18.5,13.76 18.5,12Z"
              />
            </svg>
            <svg
              v-bind:class="{ show: volumeLevel === 'muted' }"
              class="volume-muted-icon"
              viewBox="0 0 24 24"
            >
              <path
                fill="currentColor"
                d="M12,4L9.91,6.09L12,8.18M4.27,3L3,4.27L7.73,9H3V15H7L12,20V13.27L16.25,17.53C15.58,18.04 14.83,18.46 14,18.7V20.77C15.38,20.45 16.63,19.82 17.68,18.96L19.73,21L21,19.73L12,10.73M19,12C19,12.94 18.8,13.82 18.46,14.64L19.97,16.15C20.62,14.91 21,13.5 21,12C21,7.72 18,4.14 14,3.23V5.29C16.89,6.15 19,8.83 19,12M16.5,12C16.5,10.23 15.5,8.71 14,7.97V10.18L16.45,12.63C16.5,12.43 16.5,12.21 16.5,12Z"
              />
            </svg>
          </button>
          <input
            ref="volume"
            class="volume-slider"
            type="range"
            min="0"
            max="1"
            step="any"
            value="1"
            @input="onChangeVolume"
          />
        </div>
        <div class="duration-container">
          <div ref="currentTime" class="current-time">0:00</div>
          /
          <div ref="totalTime" class="total-time"></div>
        </div>
        <button class="captions-btn" @click="handleCaptionChange">
          <svg viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M18,11H16.5V10.5H14.5V13.5H16.5V13H18V14A1,1 0 0,1 17,15H14A1,1 0 0,1 13,14V10A1,1 0 0,1 14,9H17A1,1 0 0,1 18,10M11,11H9.5V10.5H7.5V13.5H9.5V13H11V14A1,1 0 0,1 10,15H7A1,1 0 0,1 6,14V10A1,1 0 0,1 7,9H10A1,1 0 0,1 11,10M19,4H5C3.89,4 3,4.89 3,6V18A2,2 0 0,0 5,20H19A2,2 0 0,0 21,18V6C21,4.89 20.1,4 19,4Z"
            />
          </svg>
        </button>
        <div id="controls">
          <button class="speed-btn wide-btn" @click="handleSpeedChange">
            {{ speed }}
          </button>
        </div>
        <button
          id="mini"
          class="mini-player-btn"
          @click="handleVideoScreenChange"
        >
          <svg viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M21 3H3c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h18c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 16H3V5h18v14zm-10-7h9v6h-9z"
            />
          </svg>
        </button>
        <button
          id="theater"
          class="theater-btn"
          @click="handleVideoScreenChange"
        >
          <svg class="tall" viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M19 6H5c-1.1 0-2 .9-2 2v8c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V8c0-1.1-.9-2-2-2zm0 10H5V8h14v8z"
            />
          </svg>
          <svg class="wide" viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M19 7H5c-1.1 0-2 .9-2 2v6c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V9c0-1.1-.9-2-2-2zm0 8H5V9h14v6z"
            />
          </svg>
        </button>
        <button
          id="full"
          class="full-screen-btn"
          @click="handleVideoScreenChange"
        >
          <svg class="open" viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M7 14H5v5h5v-2H7v-3zm-2-4h2V7h3V5H5v5zm12 7h-3v2h5v-5h-2v3zM14 5v2h3v3h2V5h-5z"
            />
          </svg>
          <svg class="close" viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M5 16h3v3h2v-5H5v2zm3-8H5v2h5V5H8v3zm6 11h2v-3h3v-2h-5v5zm2-11V5h-2v5h5V8h-3z"
            />
          </svg>
        </button>
      </div>
    </div>

    <video
      src="@/assets/Video.mp4"
      ref="video"
      @click="handleVideoPlay"
      playsinline
    >
      <track kind="captions" srclang="en" src="@/assets/subtitles.vtt" />
    </video>
  </div>
</template>
<script>
// import Vue from 'vue'

const leadingZeroFormatter = new Intl.NumberFormat(undefined, {
  minimumIntegerDigits: 2,
})

function formatDuration(time) {
  const seconds = Math.floor(time % 60)
  const minutes = Math.floor(time / 60) % 60
  const hours = Math.floor(time / 3600)
  if (hours === 0) {
    return `${minutes}:${leadingZeroFormatter.format(seconds)}`
  } else {
    return `${hours}:${leadingZeroFormatter.format(
      minutes
    )}:${leadingZeroFormatter.format(seconds)}`
  }
}

export default {
  name: 'NuxtPlayer',

  data() {
    return {
      speed: '1x',
      video: null,
      timeline: null,
      previewImg: null,
      thumbnailImg: null,
      totalTime: 0,
      currentTime: 0,
      volume: null,
      captions: null,
      isHidden: false,
      isPlaying: false,
      isScrubbing: false,
      wasPaused: null,
      volumeLevel: 'high', // high , muted, low
      screenMode: 'normal', // 'normal' , 'mini' , 'theater', 'full'
    }
  },

  mounted() {
    this.$route.query.watch
    //  vue data
    this.totalTime = this.$refs.totalTime
    this.currentTime = this.$refs.currentTime
    this.volume = this.$refs.volume
    this.video = this.$refs.video
    this.timeline = this.$refs.timeline
    this.previewImg = this.$refs.previewImg
    this.thumbnailImg = this.$refs.thumbnailImg
    this.captions = this.video.textTracks[0]
    // data handle
    this.captions.mode = 'hidden'

    // event
    this.video.addEventListener('play', () => {
      this.isPlaying = true
    })
    this.video.addEventListener('pause', () => {
      this.isPlaying = false
    })
    this.video.addEventListener('loadeddata', this.setTotal)
    this.video.addEventListener('timeupdate', this.setTimeUpdate)
    this.timeline.addEventListener('mousemove', this.handleTimelineUpdate)
    this.timeline.addEventListener('mousedown', this.toggleScrubbing)
    document.addEventListener('mouseup', (e) => {
      if (this.isScrubbing) this.toggleScrubbing(e)
    })
    document.addEventListener('mousemove', (e) => {
      if (this.isScrubbing) this.handleTimelineUpdate(e)
    })

    document.addEventListener('keydown', (e) => {
      const tagName = document.activeElement.tagName.toLowerCase()

      if (tagName === 'input') return

      switch (e.key.toLowerCase()) {
        case ' ':
          if (tagName === 'button') break
          break
        case 'k':
          this.handleVideoPlay()
          break
        case 'f':
          this.handleVideoScreenChange(e, 'full')
          break
        case 't':
          this.handleVideoScreenChange(e, 'theater')
          break
        case 'i':
          this.handleVideoScreenChange(e, 'mini')
          break
        case 'm':
          this.handleVolumeMute()
          break
        case 'arrowleft':
        case 'j':
          this.skip(-5)
          break
        case 'arrowright':
        case 'l':
          this.skip(5)
          break
        case 'c':
          this.handleCaptionChange()
          break
      }
    })
    const promise = this.video.play()
    if (promise !== undefined) {
      promise
        .then((_) => {})
        .catch((error) => {
          this.video.muted = true
          this.volumeLevel = 'muted'
          this.volume.value = 0
          this.video.play()
        })
    }
  },

  beforeDestroy() {
    this.video.removeEventListener('loadeddata', this.setTotal)
    this.video.removeEventListener('timeupdate', this.setTimeUpdate)
  },

  methods: {
    skip(duration) {
      this.video.currentTime += duration
    },
    setTimeUpdate() {
      this.currentTime.textContent = formatDuration(this.video.currentTime)
      const percent = this.video.currentTime / this.video.duration
      this.timeline.style.setProperty('--progress-position', percent)
    },
    setTotal() {
      this.totalTime.textContent = formatDuration(this.video.duration)
    },
    toggleScrubbing(e) {
      const rect = this.timeline.getBoundingClientRect()
      const percent =
        Math.min(Math.max(0, e.x - rect.x), rect.width) / rect.width
      this.isScrubbing = (e.buttons & 1) === 1
      if (this.isScrubbing) {
        this.wasPaused = this.video.paused
        this.video.pause()
      } else {
        this.video.currentTime = percent * this.video.duration
        if (!this.wasPaused) this.video.play()
      }

      this.handleTimelineUpdate(e)
    },
    handleTimelineUpdate(e) {
      const rect = this.timeline.getBoundingClientRect()
      const percent =
        Math.min(Math.max(0, e.x - rect.x), rect.width) / rect.width
      const previewImgNumber = Math.max(
        1,
        Math.floor((percent * this.video.duration) / 10)
      )
      const previewImgSrc = require(`@/assets/previewImgs/preview${previewImgNumber}.jpg`)
      this.previewImg.src = previewImgSrc
      this.timeline.style.setProperty('--preview-position', percent)

      if (this.isScrubbing) {
        e.preventDefault()
        this.thumbnailImg.src = previewImgSrc
        this.timeline.style.setProperty('--progress-position', percent)
      }
    },
    handleSpeedChange() {
      let newPlaybackRate = this.$refs.video.playbackRate + 0.25
      if (newPlaybackRate > 2) newPlaybackRate = 0.25
      this.$refs.video.playbackRate = newPlaybackRate
      this.speed = `${newPlaybackRate}x`
    },
    handleVolumeMute() {
      if (
        this.video.volume === 0 &&
        this.volumeLevel === 'muted' &&
        !this.video.muted
      ) {
        this.video.volume = 1
        this.volume.value = 1
        this.volumeLevel = 'high'
        return
      }
      this.video.muted = !this.video.muted

      this.volume.value = this.video.muted ? 0 : this.video.volume

      if (this.video.muted) {
        this.volumeLevel = 'muted'
        return
      }
      if (this.video.dataset.volumeLevel === undefined) {
        this.volumeLevel = 'high'
      } else if (this.video.dataset.volumeLevel === 'muted') {
        this.volumeLevel = 'high'
      } else {
        this.volumeLevel = this.video.dataset.volumeLevel
      }
    },
    onChangeVolume(e) {
      e.preventDefault()

      this.video.volume = e.currentTarget.value
      this.video.muted = e.currentTarget.value === 0
      if (this.video.muted || this.video.volume === 0) {
        this.volumeLevel = 'muted'
      } else if (this.video.volume >= 0.5) {
        this.volumeLevel = 'high'
      } else {
        this.volumeLevel = 'low'
      }

      this.video.dataset.volumeLevel = this.volumeLevel
    },
    handleVolumeChange() {},

    handleCaptionChange() {
      this.isHidden = this.video.textTracks[0].mode === 'hidden'
      this.video.textTracks[0].mode = this.isHidden ? 'showing' : 'hidden'
    },

    handleVideoPlay() {
      this.isPlaying = !this.isPlaying
      this.isPlaying ? this.video.play() : this.video.pause()
    },

    handleVideoScreenChange(event, key) {
      event.preventDefault()

      const current = event.currentTarget.id
      if (current === 'mini' || key === 'mini') {
        // eslint-disable-next-line no-unused-expressions
        if (this.screenMode === 'normal') {
          this.screenMode = 'mini'
        } else {
          this.screenMode = 'normal'
        }

        if (this.screenMode === 'mini') {
          this.video.requestPictureInPicture()
        } else {
          document.exitPictureInPicture()
        }
      }
      if (current === 'theater' || key === 'theater') {
        if (this.screenMode === 'normal') {
          this.screenMode = 'theater'
        } else {
          this.screenMode = 'normal'
        }
      }

      if (current === 'full' || key === 'full') {
        if (this.screenMode === 'normal') {
          this.screenMode = 'full'
        } else {
          this.screenMode = 'normal'
        }
        if (this.screenMode === 'full') {
          this.video.requestFullscreen()
        } else {
          document.exitFullscreen()
        }
      }
    },
  },
}
</script>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
}

.video-container {
  position: relative;
  width: 90%;
  max-width: 1000px;
  display: flex;
  justify-content: center;
  margin-inline: auto;
  background-color: black;
}

.video-container.theater,
.video-container.full-screen {
  max-width: initial;
  width: 100%;
}

.video-container.theater {
  max-height: 90vh;
}

.video-container.full-screen {
  max-height: 100vh;
}

video {
  width: 100%;
}

.video-controls-container {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  color: white;
  z-index: 100;
  opacity: 0;
  transition: opacity 150ms ease-in-out;
}

.video-controls-container::before {
  content: '';
  position: absolute;
  bottom: 0;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.75), transparent);
  width: 100%;
  aspect-ratio: 6 / 1;
  z-index: -1;
  pointer-events: none;
}

.video-container:hover .video-controls-container,
.video-container:focus-within .video-controls-container,
.video-container.paused .video-controls-container {
  opacity: 1;
}

.video-controls-container .controls {
  display: flex;
  gap: 0.5rem;
  padding: 0.25rem;
  align-items: center;
}

.video-controls-container .controls button {
  background: none;
  border: none;
  color: inherit;
  padding: 0;
  height: 30px;
  width: 30px;
  font-size: 1.1rem;
  cursor: pointer;
  opacity: 0.85;
  transition: opacity 150ms ease-in-out;
}

.video-controls-container .controls button:hover {
  opacity: 1;
}

.video-container.paused .pause-icon {
  display: none;
}

.video-container:not(.paused) .play-icon {
  display: none;
}

.video-container.theater .tall {
  display: none;
}

.video-container:not(.theater) .wide {
  display: none;
}

.video-container.full-screen .open {
  display: none;
}

.video-container:not(.full-screen) .close {
  display: none;
}

.volume-high-icon,
.volume-low-icon,
.volume-muted-icon {
  display: none;
}

.volume-container {
  display: flex;
  align-items: center;
}

.show {
  display: block;
}
.volume-slider {
  width: 0;
  transform-origin: left;
  transform: scaleX(0);
  transition: width 150ms ease-in-out, transform 150ms ease-in-out;
}

.volume-container:hover .volume-slider,
.volume-slider:focus-within {
  width: 100px;
  transform: scaleX(1);
}

.duration-container {
  display: flex;
  align-items: center;
  gap: 0.25rem;
  flex-grow: 1;
}

.video-container.captions .captions-btn {
  border-bottom: 3px solid red;
}

.video-controls-container .controls button.wide-btn {
  width: 50px;
}

.timeline-container {
  height: 7px;
  margin-inline: 0.5rem;
  cursor: pointer;
  display: flex;
  align-items: center;
}

.timeline {
  background-color: rgba(100, 100, 100, 0.5);
  height: 3px;
  width: 100%;
  position: relative;
}

.timeline::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  right: calc(100% - var(--preview-position) * 100%);
  background-color: rgb(150, 150, 150);
  display: none;
}

.timeline::after {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  right: calc(100% - var(--progress-position) * 100%);
  background-color: red;
}

.timeline .thumb-indicator {
  --scale: 0;
  position: absolute;
  transform: translateX(-50%) scale(var(--scale));
  height: 200%;
  top: -50%;
  left: calc(var(--progress-position) * 100%);
  background-color: red;
  border-radius: 50%;
  transition: transform 150ms ease-in-out;
  aspect-ratio: 1 / 1;
}

.timeline .preview-img {
  position: absolute;
  height: 80px;
  aspect-ratio: 16 / 9;
  top: -1rem;
  transform: translate(-50%, -100%);
  left: calc(var(--preview-position) * 100%);
  border-radius: 0.25rem;
  border: 2px solid white;
  display: none;
}

.thumbnail-img {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  display: none;
}

.video-container.scrubbing .thumbnail-img {
  display: block;
}

.video-container.scrubbing .preview-img,
.timeline-container:hover .preview-img {
  display: block;
}

.video-container.scrubbing .timeline::before,
.timeline-container:hover .timeline::before {
  display: block;
}

.video-container.scrubbing .thumb-indicator,
.timeline-container:hover .thumb-indicator {
  --scale: 1;
}

.video-container.scrubbing .timeline,
.timeline-container:hover .timeline {
  height: 100%;
}
</style>
