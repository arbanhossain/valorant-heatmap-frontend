<template>
  <FormKit type="form" :actions="false" v-model="opt" @submit="handleSubmit">
    <FormKit
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
    <FormKit type="text" label="Round No" v-model="opt.round_num" help="Space separated round numbers" />
    <FormKit type="submit" label="Submit" />
  </FormKit>
</template>

<script>
export default {
  name: "Options",
  props: ["player_list"],
  data() {
    return {
      opt: {
        player: [],
        side: [],
        time: 0,
        roundNum: "",
      },
    };
  },
  methods: {
    handleSubmit(e) {
      let rounds = []
      if(e.round_num !== "") {
        let r = e.round_num.split(" ");
        r.forEach(element => {
          rounds.push(parseInt(element) - 1)
        });
      }
      //console.log(this.opt);
      let options_object = { "player": e.player, "side": e.side, "time": e.time, "round": rounds };

      console.log(options_object);
    },
  },
};
</script>