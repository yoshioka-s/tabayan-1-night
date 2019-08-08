<template>
  <div>
    <div class="hero">
      <div>{{hero.name}}</div>
      <div>HP: {{hero.hp}}</div>
      <div>MP: {{hero.mp}}</div>
    </div>
    <div class="box">
      <div class="message" @click="next" v-if="message.length > 0">{{message}}</div>
      <ul class="commands">
        <li
          v-for="command in commands"
          :key="command"
          class="command"
          @click="select(command)"
        >{{command}}</li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import _ from "lodash";
import { Hero } from "@/classes/hero";
import { Monster } from "@/classes/monster";

@Component
export default class BattleScreen extends Vue {
  messageKey: string = "first";
  commands: Array<string> = [];
  damage: number = 0;

  @Prop() private monster!: Monster;
  @Prop() private hero!: Hero;

  get message() {
    if (!messages[this.messageKey]) {
      return "";
    }
    let text: string = messages[this.messageKey];
    while (text.indexOf("${") > -1) {
      const startIndex = text.indexOf("${");
      const stopIndex = text.indexOf("}");
      const propName = text.substring(startIndex + 2, stopIndex);
      const value = _.get(this, propName);
      text = `${text.slice(0, startIndex)}${value}${text.slice(stopIndex + 1)}`;
    }
    return text;
  }

  next() {
    console.log(this.messageKey);
    if (["first", "escape"].indexOf(this.messageKey) > -1) {
      this.commands = baseCommands;
      this.messageKey = "none";
    } else if (this.messageKey === "myTurn1") {
      this.damage = calcDamage(this.hero.attack);
      this.messageKey = "myTurn2";
    } else if (this.messageKey === "myTurn2") {
      this.monster.hp = this.monster.hp - this.damage;
      if (this.monster.hp < 1) {
        this.messageKey = "beaten";
      } else {
        this.messageKey = "theirTurn1";
      }
    } else if (this.messageKey === "theirTurn1") {
      this.damage = calcDamage(this.monster.attack);
      this.messageKey = "theirTurn2";
    } else if (this.messageKey === "theirTurn2") {
      this.hero.hp = this.hero.hp - this.damage;
      this.messageKey = "none";
      this.commands = baseCommands;
    } else if (this.messageKey === "beaten") {
      this.messageKey = "victory";
    } else if (this.messageKey === "victory") {
      this.messageKey = "first";
      this.$emit("beaten");
    }
    console.log("ok", this.messageKey);
  }
  select(command: string) {
    if (command === "にげる") {
      this.messageKey = "escape";
    } else {
      this.messageKey = "myTurn1";
    }
    this.commands = [];
  }
}

const messages = {
  first: "${monster.name} があらわれた！",
  none: "",
  escape: "にげちゃだめだ！",
  myTurn1: "${hero.name} のこうげき！",
  myTurn2: "${monster.name} に ${damage} のダメージ！",
  theirTurn1: "${monster.name} のこうげき！",
  theirTurn2: "${hero.name} に ${damage} のダメージ！",
  beaten: "${monster.name} をやっつけた！",
  victory: "100の経験値を獲得！20円手に入れた！"
};
const baseCommands = ["たたかう", "たたく", "闘う", "にげる"];
function calcDamage(attack: number) {
  console.log(
    attack,
    Math.random() * 4,
    attack + Math.random() * 4 - 2,
    Math.floor(attack + Math.random() * 4 - 2)
  );
  return Math.floor(attack + Math.random() * 4 - 2);
}
</script>

<style scoped lang="stylus">
.box {
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

.commands {
  list-style: none;

  .command {
    display: inline-block;
    width: 40%;
    cursor: pointer;
    margin: 20px;
  }
}

.message {
  width: 100%;
  height: 100%;
}
</style>
