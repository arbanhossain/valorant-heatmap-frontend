<template>
  <div class="container">
      <img :class="map_name" :src="images[map_name]" />
    <img class="plot" :src="plot_src" />
    </div>
</template>

<script>
export default {
  name: 'MapView',
  props: ['map_name', 'plot_src'],
  data(){
    return {
      images: {
        "bonsai": require('@/assets/bonsai.png'),
        "no-map": require('@/assets/no-map.png'), 
      },
    }
  }
}
</script>

<style>
.container{
  position: relative;
}
.container > img{
  position: absolute;
  top:0;
  left:0;
}
/* .container > .map{
  height: 828px;
  width: 770px;
  z-index: 2;
  opacity: 0.5;
  margin-left: 133px;
  margin-top: 0px;
} */

.container > .bonsai{
  height: 618px;
  width: 823px;
  z-index: 2;
  opacity: 0.5;
  margin-left: 200px;
  margin-top: 185px;
}

.container > .plot{
  width: 1180px;
}
</style>