<template>
  <div class="hello">
      <div class="bg" >
        

      </div>

  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      BotImage: require("../assets/bot_icon.png"),
      ColorsImage: require("../assets/colors.png"),

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.head_class{
  font-size: 30px;
}
.hello{
  position: relative;
}
.bg{
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
}

</style>
