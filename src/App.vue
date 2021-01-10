<template>
  <div id="app">
    <ColorPicker class="w-2/4 border border-black bg-white" v-model="color"/>
    <p>Current color: {{color}}</p>
  </div>
</template>

<script>
import ColorPicker from '@/components/ColorPicker'

export default {
  name: 'App',
  components: {
    ColorPicker,
  },
  data() {
    return {
      color: '#dfed00'
    }
  }
}
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: #d3d3d3;
  justify-content: center;
  align-items: center;
}
</style>
