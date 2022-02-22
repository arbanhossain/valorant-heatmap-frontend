<template>
  <FormKit type=button label="Add Match" @click="handleAddMatch" />
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
    <pre wrap>{{ opt.time }}</pre>
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
export default {
  name: "Options",
  props: ["player_list", "match_info"],
  emits: ["add-match-id"],
  data() {
    return {
      opt: {
        player: [],
        side: [],
        time: 0,
        roundNum: "",
        matches:[],
      },
    };
  },
  methods: {
    handleSubmit(e) {
      let rounds = [];
      if (e.round_num !== "") {
        let r = e.round_num.split(" ");
        r.forEach((element) => {
          rounds.push(parseInt(element) - 1);
        });
      }
      //console.log(this.opt);
      let options_object = {
        player: e.player,
        side: e.side,
        time: e.time,
        round: rounds,
      };

      console.log(options_object);
    },
    handleAddMatch(){
      let prompt = window.prompt("Enter match id");
      this.$emit('add-match-id', prompt);
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