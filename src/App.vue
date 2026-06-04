

<template>
  <h1 :class="{blue : Isturn}">{{message}}</h1>

  <input type="text" v-model="message">


  <h1>login</h1>
  <input type="text" v-model="username">
  <p>{{ username }}</p>

  
</template>



<script lang="ts">
export default {
  data () {
    return {
      username: "",
      message: "Hello Cambadia",
      Isturn: true,
    }
  }
}
</script>
<style scoped>
.blue{
  color: blue;
}
</style>
