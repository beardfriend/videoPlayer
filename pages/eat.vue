<template>
  <div class="main">
    <Header></Header>
    <div class="player_list">
      <HlsPlayer
        v-for="item in data"
        v-bind:iframe="item.play_url.hls.thumb"
        v-bind:img="item.thumb_url"
        v-bind:title="item.title"
        v-bind:channel="item.channel.name"
        v-bind:description="item.sub_title"
      ></HlsPlayer>
    </div>
    <Footer></Footer>
  </div>
</template>

<script>
import axios from 'axios'
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
  display: flex;
  flex-direction: column;
  align-items: center;

  padding: 0 10%;
  position: relative;
  min-height: 300vh;
  margin: 0;
}
</style>
