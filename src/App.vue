<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">ゲーム</router-link>|
      <router-link to="/about">スタッフロール</router-link>
    </div>
    <router-view />
  </div>
</template>
<style lang="stylus">
body {
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffff;
  background-color: black;
  min-height: 100vh;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
