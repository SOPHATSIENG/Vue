<template>
  <div class="w-6/12 m-auto px-6 py-12 border border-gray-300 rounded-md ">
    <h1 class="text-2xl font-bold uppercase text-center">Task manager App</h1>

    <form>
      <dev class="mt-8">
        <label for="task-title">Tasks tille:</label> <br>
      </dev>
      
      <input type="text" class="px-4 w-full mt-2 py-2 border border-gray-200 rounded-md">

      <label for="Task priority">Task priority:</label><br>
      <select class="px-4 w-full mt-2 py-2 border rounded md">
        <option value="Select all">Select all</option>
        <option value="higght">hight</option>
        <option value="meduid">meduim</option>
        <option value="low">low</option>
      </select>
      <button class=" w-full mt-4 bg-blue-500 hover:bg-blue-400 text-white font-bold py-2 px-4 border-b-4 border-blue-700 hover:border-blue-500 rounded">Button</button>



    </form>
  </div>
</template>

<script setup>

</script>

<style lang="scss" scoped>

</style>