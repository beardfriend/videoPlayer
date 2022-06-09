<template>
  <div class="main">
    <Header></Header>
    <div style="min-height: 100vh">
      <div class="player_list">
        <HlsPlayer
          v-for="item in data"
          :iframe="item.play_url.hls.thumb"
          :img="item.thumb_url"
          :title="item.title"
          :channel="item.channel.name"
          :description="item.sub_title"
          :profile="item.channel.channel_profile_img"
        ></HlsPlayer>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>

<script>
import axios from 'axios'
import Hls from 'hls.js'
import BootstrapVue from 'bootstrap-vue'
import Vue from 'vue'

export default {
  name: 'EatPage',
  data() {
    return {
      data: [],
    }
  },
  mounted() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      axios
        .get(
          'https://eatalk.live24.app/api/vod/json/list?row_count=15&page_no=1&order_col=no&shuffle=false'
        )
        .then((res) => {
          console.log(res)
          this.data = res.data.result.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
}
</script>

<style>
.main {
  position: relative;
  min-height: 100vh;
  margin: 0;
  padding: 0;
}
.player_list {
  margin: 0 auto;
  max-width: 1650px;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-gap: 80px 0;
  width: 100%;
  padding: 100px 0;
  position: relative;
}
@media (1370px <= width <= 1650px) {
  .player_list {
    grid-template-columns: repeat(4, 1fr);
  }
}

@media (927px <= width <= 1369px) {
  .player_list {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (639px <= width <= 926px) {
  .player_list {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (width <= 638px) {
  .player_list {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
}
</style>
