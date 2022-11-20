<template>
  <div class="home">
    <Map />
  </div>
</template>

<script>
// @ is an alias to /src
import Map from '@/components/Map.vue'

export default {
  name: 'HomeView',
  components: {
    Map
  }
}
</script>
<style lang="scss">
.home {
  display: flex;
  justify-content: center
  }
</style>
