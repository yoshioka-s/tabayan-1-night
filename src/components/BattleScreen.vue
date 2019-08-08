<template></template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
export default class BattleScreen extends Vue {
  @Prop() private monster!: object;
}
</script>

<style>
</style>
