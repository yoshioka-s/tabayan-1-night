<template>
  <div class="hello">
    <form v-if="scene.prompt==='hero.name'" @submit.prevent="next">
      <input v-model="hero.name" />
      <input type="submit" value="確定" />
      <p>{{ hero.name }}</p>
    </form>
    <form v-if="scene.options" @submit.prevent="next">
      <button v-for="(option, i) in scene.options" :key="i" @click="select(option)">{{ option }}</button>
    </form>
    <div class="text" @click="next">
      <span v-show="scene.speaker">{{ scene.speaker }}:</span>
      <div v-if="scene.aiduchi">{{aiduchi}}</div>
      {{ text }}
    </div>
    <BattleScreen v-if="scene.monster" :monster="scene.monster"></BattleScreen>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import _ from "lodash";
import { Hero } from "@/classes/hero";
import BattleScreen from "./BattleScreen.vue";

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  sceneIndex: number = 0;
  answers: Array<string> = [];

  hero: Hero = {
    name: "ヒロシ"
  };
  private next() {
    this.sceneIndex++;
  }
  get scene() {
    return scenes[this.sceneIndex];
  }
  get text() {
    let text: string = this.scene.text;
    if (text.indexOf("${") === -1) {
      return text;
    }
    const startIndex = text.indexOf("${");
    const stopIndex = text.indexOf("}");
    const propName = text.substring(startIndex + 2, stopIndex);
    const value = _.get(this, propName);
    return `${text.slice(0, startIndex)}${value}${text.slice(stopIndex + 1)}`;
  }
  select(option: string) {
    this.answers.push(option);
    this.next();
  }
  get aiduchi() {
    const index = Math.floor(Math.random() * aiduchis.length);
    return aiduchis[index];
  }
}

const scenes = [
  {
    speaker: "母",
    text: "いつまで寝てるんだい？早く起きなさい！今日は元服の儀式だろう？"
  },
  { speaker: "母", text: "ええと、お前の名前はなんだったっけ？" },
  { prompt: "hero.name", text: "あなたの名前を入力してください。" },
  {
    speaker: "母",
    text: "ああそうだったね、おまえの名前は ${hero.name} だった！"
  },
  {
    text: "今どう思いましたか？",
    options: [
      "新しい冒険にわくわくする！",
      "無理やり起こすなんてひどいな！",
      "明日晴れるといいな",
      "つまんない"
    ]
  },
  { aiduchi: true, text: "" },
  {
    speaker: "母",
    text: "外はまものがいっぱいでるらしいよ。気をつけていっておいで。"
  },
  { text: "${hero.name} は旅立った。" },
  { monster: { name: "たばやん", hp: 10, attack: 3 }, text: "" }
];
const aiduchis = [
  "ほうほう、そうきましたか。",
  "そうなんですね。",
  "まじっすか！",
  "さすがっすね。",
  "なるほどっすね。"
];
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.text {
  display: inline-block;
  border-color: #ffffff;
  border-width: 3px;
  border-style: solid;
  border-radius: 8px;
  height: 200px;
  width: 600px;
  padding: 20px;
  text-align: left;
}
</style>
