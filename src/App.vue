<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
  export default {
    name: 'App'
  }
</script>

<style lang="less">
  @import 'variable';
  html, body ,p{
    padding: 0;
    margin: 0;
  }
  ul,ol,dl,dd{
    list-style: none;
    padding: 0;
    margin: 0;
  }
  .lt{
    float:left;
  }
  .rt{
    float:right;
  }
  .clr{
    content:'';
    display: table;
    clear:both;
  }
  .flex{
    display: flex;
    flex-flow: row nowrap;
  }
  .flex-align-center{
    align-items: center;
  }
  .flex-justify-center{
    justify-content: center;
  }
</style>

