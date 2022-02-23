<template>
  <FormKit type="button" label="Add Match" @click="handleAddMatch" />
  <FormKit type="form" :actions="false" v-model="opt" @submit="handleSubmit">
    <FormKit
      v-if="match_info.length > 0"
      type="checkbox"
      label="Matches"
      :options="match_info"
      v-model="opt.matches"
    />
    <FormKit
      v-if="player_list.length > 0"
      type="checkbox"
      label="Players"
      :options="player_list"
      v-model="opt.player"
    />
    <FormKit
      type="checkbox"
      label="Side"
      :options="['ATK', 'DEF']"
      v-model="opt.side"
    />
    <FormKit
      type="range"
      v-model="opt.time"
      label="Round Time"
      min="0"
      max="200"
    />
    <pre wrap>Before {{ opt.time }} seconds</pre>
    <FormKit
      type="text"
      label="Round No"
      v-model="opt.round_num"
      help="Space separated round numbers"
    />
    <FormKit type="submit" label="Submit" />
  </FormKit>
</template>

<script>
import "@/flash.min.js";

export default {
  name: "Options",
  props: ["player_list", "match_info"],
  emits: ["add-match-id", "request-filter"],
  data() {
    return {
      opt: {
        player: [],
        side: [],
        time: 0,
        roundNum: "",
        matches: [],
      },
    };
  },
  methods: {
    handleSubmit(e) {
      let rounds = [];
      if (e.round_num !== "" && e.round_num !== undefined) {
        let r = e.round_num.split(" ");
        r.forEach((element) => {
          rounds.push(parseInt(element) - 1);
        });
      }
      //console.log(this.opt);
      let matches = e.matches;
      let map_name = "";
      let maps = []
      if (matches.length === 0) {
        FlashMessage.error("Please select at least one match", {
          theme: "dark",
        });
        return;
      } else if (matches.length > 1) {
        this.match_info.forEach((match) => {
          if (matches.includes(match.value)) {
            console.log(match);
            if (maps.includes(match.label) === false) {
              maps.push(match.label);
            }
          }
        });
        console.log(maps);
        if (maps.length > 1) {
          FlashMessage.error("Please select only one map", {
            theme: "dark",
          });
          return;
        }
      } else {
        for(let i in this.match_info){
          let match = this.match_info[i];
          if(match.value === matches[0]){
            map_name = match.label;
            break;
          }
        }
      }


      let options_object = {
        player: e.player,
        side: e.side,
        time: parseInt(e.time) * 1000,
        round: rounds,
      };

      let req_object = {
        matches: matches,
        options: options_object,
        map_name: map_name,
      };

      this.$emit("request-filter", req_object);
    },
    handleAddMatch() {
      let prompt = window.prompt("Enter match id");
      this.$emit("add-match-id", prompt);
    },
  },
};
</script>

<style>
.blue-btn {
  background-color: #0275ff;
  color: aliceblue;
  cursor: pointer;
  transition-property: filter;
  transition-duration: 0.25s;
  transition-timing-function: ease;
  transition-delay: 0s;
}
</style>