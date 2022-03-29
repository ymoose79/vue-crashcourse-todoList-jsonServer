<template>
  <header>
    <h1>{{title}}</h1>
    <!-- ---------------- one way to do it -->
    <!-- <div v-if="toggleButton">
    <Button @btn-click="$emit('btn-click')" 
    text="Add Task" color="green"/>
    </div>
      <div v-if="!toggleButton">
    <Button @btn-click="$emit('btn-click')" 
    text="Cancel" color="red"/>
    </div> -->

    <!-- ---------------- sexier way to do it -->

    <Button @btn-click="$emit('btn-click')"
    :text="toggleButton ? 'Close' : 'Add Task'"
    :color="toggleButton ? 'red' : 'green'"/>
  </header>
</template>
<script>
import Button from './Button.vue'

    export default {
        name: 'Header',
        props: {
            title: String,
            toggleButton: Boolean,
        },
        components: {
            Button
        }
    }
</script>

<style scoped>
header{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}
</style>