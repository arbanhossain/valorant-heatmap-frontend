<template>
  <div class="visualization">
    <MapView :map_name="map_name" :plot_src="plot_src" />
  </div>
    <div class="options_menu">
      <Options :player_list="player_list" />
    </div>
</template>

<script>
import Options from '@/components/Options.vue'
import MapView from '@/components/MapView.vue'

export default {
  name: 'App',
  components: {
    Options,
    MapView
  },
  data() {
    return {
      player_list: ['liquidicalpants', 'Leminscate'],
      match_id_collection: [],
      kill_data: [],
      map_name: 'no_map',
      plot_src: "",
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.options_menu {
  text-align: left;
  position: absolute;
  overflow-wrap: break-word;
  overflow-y: scroll;
  right: 0;
  width: 30%;
}
</style>
