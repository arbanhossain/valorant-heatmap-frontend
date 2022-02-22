<template>
  <div class="visualization">
    <MapView :map_name="map_name" :plot_src="plot_src" />
  </div>
  <div class="options_menu">
    <Options
      @add-match-id="add_new_match"
      :player_list="player_list"
      :match_info="match_info"
    />
  </div>
</template>

<script>
import Options from "@/components/Options.vue";
import MapView from "@/components/MapView.vue";

export default {
  name: "App",
  components: {
    Options,
    MapView,
  },
  data() {
    return {
      player_list: [],
      match_id_collection: {},
      kill_data: [],
      matches: [],
      match_info: [

      ],
      map_name: "no_map",
      plot_src: "",
    };
  },
  methods: {
    async add_new_match(id) {
      if (this.match_id_collection[id] == undefined) {
        try {
          let response = await fetch(
            "https://valorant-heatmap.herokuapp.com/api/matches/" + id,
            {
              method: "GET",
            }
          );

          let string = await response.text();
          let json = string === "" ? {} : JSON.parse(string);

          console.log(json);

          let new_match_id_collection = this.match_id_collection;
          new_match_id_collection[id] = json;
          this.match_id_collection = new_match_id_collection;

          let new_match_info = this.match_info;
          new_match_info.push({
            value: id,
            label: json["map_details"].name,
            help: id
          });
          this.match_info = new_match_info;

          let players = json["players"];

          
          let new_player_list = this.player_list;
          for (const key in players) {
            let player = players[key];
            //console.log(player)
            if(new_player_list.some( elem => elem.value === player.id )){
              console.log(player)
            } else {
              new_player_list.push({
                value: player.id,
                label: player.name,
                help: player.id
              });
            }
          }
          this.player_list = new_player_list;
          console.log(new_player_list)
        } catch (error) {
          console.error(error);
        }
      }
    },
  },
};
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
