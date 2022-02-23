<template>
  <div class="visualization">
    <MapView :map_name="map_name" :plot_src="plot_src" />
  </div>
  <div class="options_menu">
    <Options
      @add-match-id="add_new_match"
      @request-filter="request_filter"
      :player_list="player_list"
      :match_info="match_info"
    />
  </div>
</template>

<script>
import Options from "@/components/Options.vue";
import MapView from "@/components/MapView.vue";

import "@/flash.min.js";

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
      maps: [],
      matches: [],
      match_info: [],
      map_name: "no-map",
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

          let map_details = json["map_details"];

          // console.log(json);

          let new_match_id_collection = this.match_id_collection;
          new_match_id_collection[id] = json;
          this.match_id_collection = new_match_id_collection;

          let new_match_info = this.match_info;
          new_match_info.push({
            value: id,
            label: map_details.name,
            help: id,
          });
          this.match_info = new_match_info;

          let players = json["players"];

          let new_player_list = this.player_list;
          for (const key in players) {
            let player = players[key];
            //console.log(player)
            if (new_player_list.some((elem) => elem.value === player.id)) {
              console.log(player);
            } else {
              new_player_list.push({
                value: player.id,
                label: player.name,
                help: player.id,
              });
            }
          }
          this.player_list = new_player_list;
          console.log(new_player_list);

          let map_list = this.maps;
          if (map_list.some( map => map.name === map_details.name) === false) {
            map_list.push(map_details);
          }
          this.maps = map_list;

        } catch (error) {
          console.error(error);
        }

      }
    },
    async request_filter(obj) {
      let matches = obj["matches"];
      let options = obj["options"];
      let map_name = obj["map_name"];

      console.log(map_name);

      let combined_kill_data = [];
      matches.forEach((match) => {
        console.log(match);
        let kill_data = Array.from(
          this.match_id_collection[match]["kill_data"]
        );
        kill_data.forEach((kill) => {
          combined_kill_data.push(kill);
        });
      });

      let filter_req = { kill_data: combined_kill_data, options: options };

      // console.log(JSON.stringify(filter_req));

      try {
        let response = await fetch(
          "https://valorant-heatmap.herokuapp.com/api/utils/filter",
          {
            method: "POST",
            headers: {
              "Content-Type": "text/plain",
            },
            body: JSON.stringify(filter_req),
            // mode: 'cors',
          }
        );

        let string = await response.text();

        let json = string === "" ? {} : JSON.parse(string);

        let filtered_data = json["data"];
        
        let map = this.maps.filter(map => map.name === map_name)[0];
  
        let plot_req = { data: filtered_data, map_details: map };

        response = await fetch(
          "https://valorant-heatmap.herokuapp.com/api/utils/plot",
          {
            method: "POST",
            headers: {
              "Content-Type": "text/plain",
            },
            body: JSON.stringify(plot_req),
            // mode: 'cors',
          }
        );

        string = await response.text();
        json = string === "" ? {} : JSON.parse(string);

        this.plot_src = `data:image/svg+xml;base64,${json["data"]}`;
        this.map_name = map.id;
        console.log(this.plot_src, this.map_name);
        console.log("plotted");
      } catch (error) {
        console.error(error);
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
