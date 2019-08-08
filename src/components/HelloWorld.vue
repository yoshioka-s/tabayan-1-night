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
    <div class="text" @click="next" v-if="!scene.monster">
      <span v-show="scene.speaker">{{ scene.speaker }}:</span>
      <div v-if="scene.aiduchi">{{aiduchi}}</div>
      {{ text }}
    </div>
    <BattleScreen v-if="scene.monster" :monster="scene.monster" :hero="hero" @beaten="next"></BattleScreen>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import _ from "lodash";
import { Hero } from "@/classes/hero";
import BattleScreen from "./BattleScreen.vue";

@Component({
  components: {
    BattleScreen
  }
})
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  sceneIndex: number = 0;
  answers: Array<string> = [];

  hero: Hero = {
    name: "ヒロシ",
    hp: 15,
    mp: 0,
    attack: 5
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
    const index =
      Math.floor(Math.random() * aiduchis.length + this.sceneIndex) %
      aiduchis.length;
    return aiduchis[index];
  }
  get result() {
    const adjs = ["勇敢な", "臆病な", "心優しい", "穏やかな", "にぎやかな"];
    const nouns = ["戦士", "魔法使い", "牧師", "遊び人", "スライム"];
    const index1 = Math.floor(Math.random() * adjs.length);
    const index2 = Math.floor(Math.random() * nouns.length);
    return `${adjs[index1]} ${nouns[index2]}`;
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
      "もうちょい眠りたいな",
      "無理やり起こすなんてひどいな！",
      "今日晴れるといいな",
      "つまんない"
    ]
  },
  { aiduchi: true, text: "" },
  {
    speaker: "母",
    text: "外はまものがいっぱいでるらしいよ。気をつけていっておいで。"
  },
  { text: "${hero.name} は旅立った。" },
  {
    text: "今どう思いましたか？",
    options: [
      "新しい冒険にわくわくする！",
      "どこにいけばいいのさ",
      "まものこわいな",
      "つまんない"
    ]
  },
  { aiduchi: true, text: "" },
  { monster: { name: "たばやん", hp: 10, attack: 3 }, text: "" },
  {
    text: "今どう思いましたか？",
    options: [
      "殺しは楽しいぜ",
      "まものがかわいそう",
      "もっと強くなりたい",
      "つまんない"
    ]
  },
  { aiduchi: true, text: "" },
  { text: "診断結果が出ました。" },
  { text: "あなたは ${result} ですね。" }
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
