<template>
  <div>
    <nuxt-link :to="{ path: '/video' }">
      <button>user interaction</button>
    </nuxt-link>
    <nuxt-link :to="{ path: '/eat' }">
      <button>리스트</button>
    </nuxt-link>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
}
</script>
